---
title: Popisy rolí a oprávnění správce – Azure AD | Dokumenty společnosti Microsoft
description: Role správce může přidávat uživatele, přiřazovat role pro správu, resetovat uživatelská hesla, spravovat uživatelské licence nebo spravovat domény.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfe8aa088538663ac3e64f5913ff031e6160b045
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382640"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Oprávnění role správce v Azure Active Directory

Pomocí služby Azure Active Directory (Azure AD) můžete určit omezené správce pro správu úloh identit v méně privilegovaných rolích. Správci mohou být přiřazeni k takovým účelům, jako je přidávání nebo změna uživatelů, přiřazování rolí pro správu, resetování uživatelských hesel, správa uživatelských licencí a správa názvů domén. Výchozí uživatelská oprávnění lze změnit jenom v uživatelských nastaveních ve službě Azure AD.

## <a name="limit-use-of-global-administrator"></a>Omezit použití globálního správce

Uživatelé, kteří jsou přiřazeni k roli globálního správce, můžou číst a upravovat všechna nastavení správy ve vaší organizaci Azure AD. Ve výchozím nastavení je osobě, která se zaregistruje k předplatnému Azure, přiřazena role globálního správce pro organizaci Azure AD. Role správce mohou delegovat pouze globální správci a správci privilegovaných rolí. Chcete-li snížit riziko pro vaši firmu, doporučujeme přiřadit tuto roli co nejméně lidí ve vaší organizaci.

Jako osvědčený postup doporučujeme přiřadit tuto roli méně než pěti lidem ve vaší organizaci. Pokud máte více než pět správců přiřazených k roli globálního správce ve vaší organizaci, zde je několik způsobů, jak snížit jeho používání.

### <a name="find-the-role-you-need"></a>Najděte roli, kterou potřebujete

Pokud je pro vás frustrující najít roli, kterou potřebujete, ze seznamu mnoha rolí, Azure AD vám může zobrazit podmnožiny rolí na základě kategorií rolí. Podívejte se na náš **nový** typ filtru pro role [Azure AD a správci](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) zobrazit pouze role ve vybraném typu.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Nyní existuje role, která neexistovala, když jste přiřadili roli globálního správce

Je možné, že role nebo role byly přidány do Azure AD, které poskytují podrobnější oprávnění, která nebyla možnost, když jste zvýšilněkteří uživatelé na globálního správce. V průběhu času zavádíme další role, které provádějí úkoly, které dříve mohla provádět pouze role globálního správce. Tyto projevy se projeví v následujících [rolích Dostupné](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Přiřazení nebo odebrání rolí správce

Informace o tom, jak přiřadit role pro správu uživateli ve službě Azure Active Directory, najdete v tématu [Zobrazení a přiřazení rolí správce ve službě Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Dostupné role

K dispozici jsou následující role správce:

### <a name="application-administrator"></a>[Správce aplikace](#application-administrator-permissions)

Uživatelé v této roli mohou vytvářet a spravovat všechny aspekty podnikových aplikací, registrací aplikací a nastavení proxy serveru aplikace. Všimněte si, že uživatelé přiřazené k této roli nejsou přidány jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

Správci aplikací mohou spravovat pověření aplikace, která jim umožňují zosobnit aplikaci. Uživatelé přiřazení k této roli tedy mohou spravovat přihlašovací údaje aplikace pouze těch aplikací, které nejsou přiřazeny k žádným rolím Azure AD, nebo těm, které jsou přiřazeny jenom k následujícím rolím správce:
* Správce aplikace
* Vývojář aplikace
* Správce cloudové aplikace
* Čtečky adresářů

Pokud je aplikace přiřazena k jiné roli, která není uvedena výše, správce aplikace nemůže spravovat pověření této aplikace. 
 
Tato role také uděluje možnost _souhlasu s_ delegovanými oprávněními a oprávněními aplikací, s výjimkou oprávnění k rozhraní Microsoft Graph API.

> [!IMPORTANT]
> Tato výjimka znamená, že můžete stále souhlasit s oprávněními pro _jiné_ aplikace (například aplikace třetích stran nebo aplikace, které jste zaregistrovali), ale ne oprávnění pro samotné azure ad. Stále můžete _požádat o_ tato oprávnění jako součást registrace aplikace, ale _udělení_ (tj. souhlas) tato oprávnění vyžaduje správce Azure AD. To znamená, že uživatel se zlými úmysly nemůže snadno zvýšit svá oprávnění, například vytvořením a souhlasem s aplikací, která může zapisovat do celého adresáře, a prostřednictvím oprávnění této aplikace se mohou zvýšit a stát se globálním správcem.

### <a name="application-developer"></a>[Vývojář aplikace](#application-developer-permissions)

Uživatelé v této roli mohou vytvářet registrace aplikací, pokud je nastavení "Uživatelé mohou registrovat aplikace" nastaveno na ne. Tato role také uděluje oprávnění k udělení souhlasu vlastním jménem, když je nastavení "Uživatelé mohou souhlasit s aplikacemi, které přistupují k firemním datům jejich jménem", nastaveno na ne. Uživatelé přiřazení k této roli jsou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

### <a name="authentication-administrator"></a>[Správce ověřování](#authentication-administrator-permissions)

Role správce ověřování je aktuálně ve verzi Public Preview. Uživatelé s touto rolí mohou nastavit nebo obnovit pověření bez hesla a mohou aktualizovat hesla pro všechny uživatele. Správci ověřování mohou požadovat, aby se uživatelé znovu zaregistrovali podle existujících pověření bez hesla (například Vícefaktorové ověřování nebo FIDO) a odvolat **zapamatovat si vícefaktorové ověřování v zařízení**, což vyzve k vícefaktorové ověřování při příštím přihlášení uživatelů, kteří nejsou správci nebo mají přiřazeny pouze následující role:

* Správce ověřování
* Čtečky adresářů
* Pozvaní hosté
* Čtečka Centra zpráv
* Čtečka sestav

> [!IMPORTANT]
> Uživatelé s touto rolí můžete změnit přihlašovací údaje pro uživatele, kteří mohou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i vně služby Azure Active Directory. Změna pověření uživatele může znamenat možnost převzít identitu a oprávnění tohoto uživatele. Příklad:
>
>- Vlastníci aplikací a podnikových aplikací, kteří mohou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace mohou mít privilegovaná oprávnění ve službě Azure AD a jinde, která nejsou udělena správcům ověřování. Prostřednictvím této cesty může být správce ověřování schopen převzít identitu vlastníka aplikace a dále převzít identitu privilegované aplikace aktualizací pověření pro aplikaci.
>- Vlastníci předplatného Azure, kteří mohou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
>- Security Group a Office 365 Group vlastníci, kteří mohou spravovat členství ve skupině. Tyto skupiny mohou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci ve službě Azure AD i jinde.
>- Správci v jiných službách mimo Azure AD, jako je Exchange Online, Office Security and Compliance Center a systémy lidských zdrojů.
>- Nesprávci, jako jsou vedoucí pracovníci, právní zástupci a zaměstnanci v oblasti lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

### <a name="azure-devops-administrator"></a>[Správce Azure DevOps](#azure-devops-administrator-permissions)

Uživatelé s touto rolí můžete spravovat zásady Azure DevOps omezit nové Azure DevOps organizace vytváření na sadu konfigurovatelných uživatelů nebo skupin. Uživatelé v této roli můžete spravovat tyto zásady prostřednictvím libovolné organizace Azure DevOps, která je zálohována organizace Azure AD společnosti.

Všechny podnikové zásady Azure DevOps mohou spravovat uživatelé v této roli.

### <a name="azure-information-protection-administrator"></a>[Správce ochrany informací Azure](#azure-information-protection-administrator-permissions)

Uživatelé s touto rolí mají všechna oprávnění ve službě Azure Information Protection. Tato role umožňuje konfiguraci popisků pro zásady ochrany informací Azure, správu šablon ochrany a aktivaci ochrany. Tato role neuděluje žádná oprávnění v Centru ochrany identit, Privilegované správě identit, Monitoru stavu služby Office 365 nebo Centru zabezpečení Office 365 & compliance.

### <a name="b2c-user-flow-administrator"></a>[Správce toku uživatelů B2C](#b2c-user-flow-administrator-permissions)

Uživatelé s touto rolí můžete vytvářet a spravovat Toky uživatelů B2C (označované také jako "integrované" zásady) na webu Azure Portal.Vytvořením nebo úpravou uživatelských toků mohou tito uživatelé změnit obsah html/CSS/javascript uživatelského prostředí, změnit požadavky mfa na tok uživatele, změnit deklarace identity v tokenu a upravit nastavení relace pro všechny zásady v tenantovi. Na druhé straně tato role nezahrnuje možnost zkontrolovat uživatelská data nebo provést změny atributů, které jsou zahrnuty ve schématu klienta.Změny identity experience framework (označované také jako vlastní) zásady je také mimo rozsah této role.

### <a name="b2c-user-flow-attribute-administrator"></a>[Správce atributů toku uživatele B2C](#b2c-user-flow-attribute-administrator-permissions)

Uživatelé s touto rolí přidat nebo odstranit vlastní atributy, které jsou k dispozici pro všechny toky uživatelů v tenantovi.Jako takové uživatelé s touto rolí můžete změnit nebo přidat nové prvky do schématu koncového uživatele a ovlivnit chování všech toků uživatelů a nepřímo vést ke změnám, jaká data mohou být požádáni o koncové uživatele a nakonec odeslány jako deklarace identity aplikací.Tato role nemůže upravovat toky uživatelů.

### <a name="b2c-ief-keyset-administrator"></a>[Správce sady klíčů B2C IEF](#b2c-ief-keyset-administrator-permissions)

Uživatel může vytvářet a spravovat klíče zásad a tajné klíče pro šifrování tokenů, podpisy tokenů a deklarace šifrování/dešifrování.Přidáním nových klíčů do existujících kontejnerů klíčů, tento omezený správce můžete převrácení tajných kódů podle potřeby bez dopadu na existující aplikace.Tento uživatel může vidět úplný obsah těchto tajemství a jejich data vypršení platnosti i po jejich vytvoření.

> [!IMPORTANT]
> Tohle je citlivá role.Role správce sady klíčů by měla být pečlivě auditována a přidělována s opatrností během předprodukční a produkční.

### <a name="b2c-ief-policy-administrator"></a>[Správce zásad B2C IEF](#b2c-ief-policy-administrator-permissions)

Uživatelé v této roli mají možnost vytvářet, číst, aktualizovat a odstraňovat všechny vlastní zásady v Azure AD B2C a proto mají plnou kontrolu nad rozhraním identity experience framework v příslušném tenantovi Azure AD B2C. Úpravami zásad může tento uživatel vytvořit přímou federaci s externími poskytovateli identit, změnit schéma adresáře, změnit veškerý obsah orientovaný na uživatele (HTML, CSS, JavaScript), změnit požadavky na dokončení ověřování, vytvořit nové uživatele, odeslat uživatelská data do externích systémů včetně úplných migrací a upravit všechny informace o uživateli včetně citlivých polí, jako jsou hesla a telefonní čísla. Naopak tato role nemůže změnit šifrovací klíče nebo upravit tajné klíče používané pro federaci v tenantovi.

> [!IMPORTANT]
> Správce zásad B2 IEF je vysoce citlivá role, která by měla být přidělena na velmi omezeném základě pro nájemníky ve výrobě.Aktivity těchto uživatelů by měly být pečlivě auditovány, zejména pro klienty ve výrobě.

### <a name="billing-administrator"></a>[Správce fakturace](#billing-administrator-permissions)

Může dělat nákupy, spravovat předplatná, spravovat lístky žádostí o podporu a sledovat stav služeb.

### <a name="cloud-application-administrator"></a>[Správce cloudové aplikace](#cloud-application-administrator-permissions)

Uživatelé v této roli mají stejná oprávnění jako role Správce aplikace, s výjimkou možnosti spravovat proxy aplikace. Tato role umožňuje vytvářet a spravovat všechny aspekty podnikových aplikací a registrací aplikací. Tato role také uděluje možnost souhlasu s delegovanými oprávněními a oprávněními aplikací s výjimkou rozhraní Microsoft Graph API. Uživatelé přiřazení k této roli nejsou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

Správci cloudových aplikací mohou spravovat pověření aplikace, která jim umožňují zosobnit aplikaci. Uživatelé přiřazení k této roli tedy mohou spravovat přihlašovací údaje aplikace pouze těch aplikací, které nejsou přiřazeny k žádným rolím Azure AD, nebo těm, které jsou přiřazeny jenom k následujícím rolím správce:
* Vývojář aplikace
* Správce cloudové aplikace
* Čtečky adresářů

Pokud je aplikace přiřazena k jiné roli, která není uvedena výše, správce cloudových aplikací nemůže spravovat pověření této aplikace.

### <a name="cloud-device-administrator"></a>[Správce cloudových zařízení](#cloud-device-administrator-permissions)

Uživatelé v této roli můžete povolit, zakázat a odstranit zařízení ve službě Azure AD a číst windows 10 BitLocker klíče (pokud je k dispozici) na webu Azure Portal. Role neuděluje oprávnění ke správě jiných vlastností v zařízení.

### <a name="compliance-administrator"></a>[Správce dodržování předpisů](#compliance-administrator-permissions)

Uživatelé s touto rolí mají oprávnění ke správě funkcí souvisejících s dodržováním předpisů v Centru dodržování předpisů Microsoftu 365, centru pro správu Microsoftu 365, Azure a Centru zabezpečení office 365 & compliance. Postupníci můžou taky spravovat všechny funkce v centru pro správu Exchange a Teams & centra pro správu Skypu pro firmy a vytvářet lístky podpory pro Azure a Microsoft 365. Další informace jsou k dispozici na [stránce Role správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

V | Může dělat
----- | ----------
[Centrum dodržování předpisů Microsoftu 365](https://protection.office.com) | Ochrana a správa dat vaší organizace ve službách Microsoft 365<br>Správa výstrah dodržování předpisů
[Správce dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Sledování, přiřazení a ověření činností organizace v oblasti dodržování předpisů
[Centrum dodržování předpisů pro zabezpečení & Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa správy dat<br>Provádět právní a datové šetření<br>Spravovat požadavek subjektu údajů<br><br>Tato role má stejná oprávnění jako [skupina rolí správce dodržování předpisů](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) v centru zabezpečení Office 365 &, které řídí přístup založený na rolích Centra dodržování předpisů.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazení všech dat auditu Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.<br>Můžete vytvořit a upravit zásady souborů a povolit akce zásad správného řízení souborů.<br>Můžete zobrazit všechny předdefinované sestavy v části Správa dat

### <a name="compliance-data-administrator"></a>[Správce dat pro dodržování předpisů](#compliance-data-administrator-permissions)

Uživatelé s touto rolí mají oprávnění ke sledování dat v Centru dodržování předpisů Microsoftu 365, Centru pro správu Microsoftu 365 a Azure. Uživatelé můžou taky sledovat data o dodržování předpisů v Centru pro správu Exchange, Správci dodržování předpisů a týmech & centra pro správu Skypu pro firmy a vytvářet lístky podpory pro Azure a Microsoft 365.

V | Může dělat
----- | ----------
[Centrum dodržování předpisů Microsoftu 365](https://protection.office.com) | Sledování zásad souvisejících s dodržováním předpisů ve službách Microsoft 365<br>Správa výstrah dodržování předpisů
[Správce dodržování předpisů](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Sledování, přiřazení a ověření činností organizace v oblasti dodržování předpisů
[Centrum dodržování předpisů pro zabezpečení & Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa správy dat<br>Provádět právní a datové šetření<br>Spravovat požadavek subjektu údajů<br><br>Tato role má stejná oprávnění jako [skupina rolí správce dat dodržování předpisů](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) v centru zabezpečení Office 365 & řízení přístupu založeného na rolích centra dodržování předpisů.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazení všech dat auditu Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.<br>Můžete vytvořit a upravit zásady souborů a povolit akce zásad správného řízení souborů.<br>Můžete zobrazit všechny předdefinované sestavy v části Správa dat

### <a name="conditional-access-administrator"></a>[Správce podmíněného přístupu](#conditional-access-administrator-permissions)

Uživatelé s touto rolí mají možnost spravovat nastavení podmíněného přístupu služby Azure Active Directory.
> [!NOTE]
> Chcete-li nasadit zásady podmíněného přístupu Exchange ActiveSync v Azure, musí být uživatel také globálním správcem.

### <a name="customer-lockbox-access-approver"></a>[Schvalovatel přístupu k bezpečnostnímu schránku zákazníka](#customer-lockbox-access-approver-permissions)

Spravuje [požadavky bezpečnostního schránku zákazníků](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) ve vaší organizaci. Dostávají e-mailová oznámení pro žádosti customer lockbox a mohou schvalovat a zamítat žádosti z Centra pro správu Microsoftu 365. Mohou také zapnout nebo vypnout funkci Customer Lockbox. Hesla osob přiřazených k této roli mohou resetovat pouze globální správci.

### <a name="desktop-analytics-administrator"></a>[Správce analýzy plochy](#desktop-analytics-administrator-permissions)


Uživatelé v této roli mohou spravovat služby & zásad pro stolní počítače a vlastní nastavení office. V případě desktopové analýzy to zahrnuje možnost zobrazit inventář datových zdrojů, vytvořit plány nasazení, zobrazit nasazení a stav stavu. U služby & zásad vlastního nastavení sady Office tato role umožňuje uživatelům spravovat zásady sady Office.

### <a name="device-administrator"></a>[Správce zařízení](#device-administrators-permissions)

Tato role je k dispozici pouze pro přiřazení jako další místní správce v [nastavení zařízení](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Uživatelé s touto rolí se stanou správci místních počítačů na všech zařízeních s Windows 10, která jsou připojená ke službě Azure Active Directory. Nemají možnost spravovat objekty zařízení ve službě Azure Active Directory.

### <a name="directory-readers"></a>[Čtečky adresářů](#directory-readers-permissions)

Uživatelé v této roli mohou číst základní informace o adresáři. Tato role by měla být použita pro:
* Udělení určité sady přístupu pro čtení uživatelů typu Host namísto udělení všem uživatelům typu Host.
* Udělení konkrétní sady uživatelů bez oprávnění správce přístup u webu Azure Portal při "Omezit přístup k portálu Azure AD pouze pro správce" je nastavena na "Ano".
* Udělení přístupu k objektům zabezpečení služby adresáři, kde directory.Read.All není možnost.

### <a name="directory-synchronization-accounts"></a>[Účty synchronizace adresářů](#directory-synchronization-accounts-permissions)

Nepoužívat. Tato role se automaticky přiřadí ke službě Azure AD Connect a není určena ani podporována pro jiné použití.

### <a name="directory-writers"></a>[Adresář spisovatelů](#directory-writers-permissions)

Toto je starší role, která má být přiřazena k aplikacím, které nepodporují [rámec pro souhlas](../develop/quickstart-register-app.md). Neměl by být přiřazen žádnému uživateli.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Správce dynamics 365 / správce aplikace CRM](#crm-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci aplikace Microsoft Dynamics 365 Online, pokud je služba k dispozici, stejně jako možnost spravovat lístky podpory a sledovat stav služby. Další informace na [stránce Použití role správce služby ke správě klienta](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> V rozhraní Microsoft Graph API a prostředí Azure AD PowerShell je tato role označena jako "Správce služby Dynamics 365". Je to "Správce Dynamics 365" na [webu Azure Portal](https://portal.azure.com).

### <a name="exchange-administrator"></a>[Správce serveru Exchange](#exchange-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci služby Microsoft Exchange Online, pokud je služba k dispozici. Má také možnost vytvářet a spravovat všechny skupiny Office 365, spravovat lístky podpory a sledovat stav služby. Další informace na stránce [O rolích správců Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> V rozhraní Microsoft Graph API a Prostředí Azure AD PowerShell je tato role označena jako "Správce exchange služby". Je to "Exchange Správce" na [webu Azure Portal](https://portal.azure.com). V [Centru pro správu Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144)se jedná o správce Exchange .

### <a name="external-identity-provider-administrator"></a>[Správce externího zprostředkovatele identity](#external-identity-provider-administrator-permissions)

Tento správce spravuje federaci mezi klienty služby Azure Active Directory a externími poskytovateli identit.Pomocí této role mohou uživatelé přidávat nové zprostředkovatele identit a konfigurovat všechna dostupná nastavení (např. cesta ověřování, ID služby, přiřazené kontejnery klíčů).Tento uživatel může povolit klientovi důvěřovat ověřování od externích poskytovatelů identity.Výsledný dopad na prostředí koncových uživatelů závisí na typu klienta:

* Tenanti Azure Active Directory pro zaměstnance a partnery: Přidání federace (například s Gmailem) bude mít okamžitě vliv na všechny pozvánky pro hosty, které ještě nebyly uplatněny. Viz [Přidání Google jako poskytovatele identity pro uživatele typu Host B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Tenantové Služby Azure Active Directory B2C: Přidání federace (například s Facebookem nebo s jinou organizací Azure AD) nemá okamžitý dopad na toky koncových uživatelů, dokud se poskytovatel identity nepřidá jako možnost v toku uživatele (označované také jako předdefinované zásady). Příklad [najdete v tématu Konfigurace účtu Microsoft jako poskytovatele identity.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app)Chcete-li změnit toky uživatelů, je vyžadována omezená role "Správce toku uživatelů B2C".

### <a name="global-administrator--company-administrator"></a>[Globální správce / správce společnosti](#company-administrator-permissions)

Uživatelé s touto rolí mají přístup ke všem funkcím správy ve službě Azure Active Directory a ke službám, které používají identity Služby Azure Active Directory, jako je Centrum zabezpečení Microsoft 365, Centrum dodržování předpisů Microsoft 365, Exchange Online, SharePoint Online a Skype pro firmy online. Osoba, která se zaregistruje do klienta Služby Azure Active Directory, se stane globálním správcem. Ve vaší společnosti může být více než jeden globální správce. Globální správci můžou resetovat heslo kteréhokoliv uživatele a všech ostatních správců.

> [!NOTE]
> V rozhraní Microsoft Graph API a Prostředí Azure AD PowerShell je tato role označena jako "Správce společnosti". Je to "globální správce" na [webu Azure Portal](https://portal.azure.com).
>
>

### <a name="global-reader"></a>[Globální čtečka](#global-reader-permissions)

Uživatelé v této roli mohou číst informace o nastavení a správě ve službách Microsoft 365, ale nemohou akce správy přijímat. Globální čtenář je protějšek globálního správce jen pro čtení. Přiřaďte globální čtečku místo globálního správce pro plánování, audity nebo šetření. Pomocí globálního čtenáře v kombinaci s dalšími omezenými rolemi správce, jako je správce serveru Exchange, usnadníte práci bez přiřazení role globálního správce. Globální čtečka spolupracuje s Centrem pro správu Microsoftu 365, Centrem pro správu Exchange, Centrem pro správu SharePointu, Centrem pro správu Teams, Centrem zabezpečení, Centrem pro dodržování předpisů, Centrem pro správu Azure AD a Centrem pro správu zařízení.

> [!NOTE]
> Globální čtenářská role má právě teď několik omezení -
>
>- [Centrum pro správu OneDrivu](https://admin.onedrive.com/) – Centrum pro správu OneDrivu nepodporuje roli globálního čtenáře.
>- [Portál Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) – globální čtenář nemůže číst zřizovací režim podnikové aplikace.
>- [Centrum pro správu M365](https://admin.microsoft.com/Adminportal/Home#/homepage) – Globální čtenář nemůže číst požadavky bezpečnostního systému zákazníků. V levém podokně Centra pro správu M365 nenajdete kartu **Požadavky bezpečnostního oken zákazníka** v části **Podpora.**
>- [M365 Security Center](https://security.microsoft.com/homepage) - Globální čtečka neumí číst popisky citlivosti a uchovávání. V levém podokně centra zabezpečení M365 nenajdete **popisky citlivosti**, **popisky uchovávání**informací a **karty Analýzy popisků.**
>- [Office Security & Centrum dodržování předpisů](https://sip.protection.office.com/homepage) – globální čtenář nemůže číst protokoly auditu SCC, prohledávat obsah nebo vidět zabezpečené skóre.
>- [Centrum pro správu Teams](https://admin.teams.microsoft.com) – Globální čtenář nemůže číst **životní cyklus teams**, sestavy & **Analytics**, **správa IP telefonních zařízení** a katalog **aplikací**.
>- [Správa privilegovaného přístupu (PAM)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) nepodporuje roli globálního čtenáře.
>- [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) – globální čtečka je podporovaná [jenom pro centrální vytváření sestav](https://docs.microsoft.com/azure/information-protection/reports-aip) a když vaše organizace Azure AD není na [platformě jednotného označování](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
>
> Tyto funkce jsou v současné době ve vývoji.
>

### <a name="groups-administrator"></a>[Správce skupin](#groups-administrator-permissions)

Uživatelé v této roli můžete vytvářet nebo spravovat skupiny a jeho nastavení, jako je pojmenování a vypršení platnosti zásady. Je důležité si uvědomit, že přiřazení uživatele k této roli mu dává možnost spravovat všechny skupiny v tenantovi napříč různými úlohami, jako jsou Teams, SharePoint, Yammer kromě Outlooku. Uživatel bude také moct spravovat různá nastavení skupin na různých portálech pro správu, jako je Microsoft Admin Center, Portál Azure, stejně jako ty specifické pro úlohy, jako jsou Teams a SharePoint Admin Centers.

### <a name="guest-inviter"></a>[Pozvaní hosté](#guest-inviter-permissions)

Uživatelé v této roli můžete spravovat Azure Active Directory B2B pozvánky uživatele typu Host, pokud **členové můžete pozvat** uživatele nastavení je nastavena na ne. Další informace o spolupráci B2B na [o spolupráci Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Neobsahuje žádná další oprávnění.

### <a name="helpdesk-administrator"></a>[Správce technické podpory](#helpdesk-administrator-permissions)

Uživatelé s touto rolí mohou měnit hesla, zneplatnit obnovovací tokeny, spravovat požadavky na služby a sledovat stav služby. Zrušení platnosti aktualizačního tokenu vynutí, aby se uživatel znovu přihlásil. Správci technické podpory mohou resetovat hesla a zneplatnit obnovovací tokeny jiných uživatelů, kteří nejsou správci nebo mají přiřazeny pouze následující role:

* Čtečky adresářů
* Pozvaní hosté
* Správce technické podpory
* Čtečka Centra zpráv
* Čtečka sestav

> [!IMPORTANT]
> Uživatelé s touto rolí můžete změnit hesla pro uživatele, kteří mohou mít přístup k citlivým nebo soukromým informacím nebo kritické konfigurace uvnitř i vně služby Azure Active Directory. Změna hesla uživatele může znamenat možnost převzít identitu a oprávnění tohoto uživatele. Příklad:
>
>- Vlastníci aplikací a podnikových aplikací, kteří mohou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace mohou mít privilegovaná oprávnění ve službě Azure AD a jinde, která nejsou udělena správcům helpdesku. Prostřednictvím této cesty může být správce technické podpory schopen převzít identitu vlastníka aplikace a dále převzít identitu privilegované aplikace aktualizací pověření pro aplikaci.
>- Vlastníci předplatného Azure, kteří mohou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
>- Security Group a Office 365 Group vlastníci, kteří mohou spravovat členství ve skupině. Tyto skupiny mohou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci ve službě Azure AD i jinde.
>- Správci v jiných službách mimo Azure AD, jako je Exchange Online, Office Security and Compliance Center a systémy lidských zdrojů.
>- Nesprávci, jako jsou vedoucí pracovníci, právní zástupci a zaměstnanci v oblasti lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

Delegování oprávnění správce na podmnožiny uživatelů a použití zásad na podmnožinu uživatelů je možné s [jednotkami pro správu (nyní ve verzi Public Preview).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)

Tato role se dříve nazývala "Správce hesel" na [webu Azure Portal](https://portal.azure.com/). Název "Helpdesk Administrator" ve službě Azure AD teď odpovídá jeho názvu v Prostředí Azure AD PowerShell a rozhraní Microsoft Graph API.

### <a name="intune-administrator"></a>[Správce Intune](#intune-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci služby Microsoft Intune Online, pokud je služba k dispozici. Kromě toho tato role obsahuje možnost spravovat uživatele a zařízení za účelem přidružení zásad, stejně jako vytvářet a spravovat skupiny. Další informace na [ovládací msa (RBAC) založené na rolích s Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).

Tato role může vytvořit a spravovat všechny skupiny zabezpečení. Správce Intune však nemá práva správce ke skupinám Office. To znamená, že správce nemůže aktualizovat vlastníky nebo členství ve všech skupinách Office v tenantovi. Může však spravovat skupinu Office, kterou vytvoří a která je součástí jeho oprávnění pro koncového uživatele. Takže všechny skupiny office (není skupina zabezpečení), které on / ona vytvoří by měly být započítány do jeho / její kvóta 250.

> [!NOTE]
> V rozhraní Microsoft Graph API a Prostředí Azure AD PowerShell je tato role označena jako "Správce služby Intune ". Je to "Správce Intune" na [webu Azure Portal](https://portal.azure.com).

### <a name="kaizala-administrator"></a>[Kaizala Správce](#kaizala-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění ke správě nastavení v rámci služby Microsoft Kaizala, pokud je služba k dispozici, stejně jako možnost spravovat lístky podpory a sledovat stav služby. Kromě toho může uživatel přistupovat k sestavám souvisejícím s přijetím & použití Kaizaly členy organizace a obchodními sestavami generovanými pomocí akcí Kaizala.

### <a name="license-administrator"></a>[Správce licencí](#license-administrator-permissions)

Uživatelé v této roli mohou přidávat, odebírat a aktualizovat přiřazení licencí pro uživatele, skupiny (pomocí skupinových licencí) a spravovat umístění využití uživatelů. Role neuděluje možnost zakoupit nebo spravovat předplatná, vytvářet nebo spravovat skupiny nebo vytvářet nebo spravovat uživatele mimo umístění využití. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="message-center-privacy-reader"></a>[Čtečka ochrany osobních údajů Centra zpráv](#message-center-privacy-reader-permissions)

Uživatelé v této roli mohou sledovat všechna oznámení v Centru zpráv, včetně zpráv o ochraně osobních údajů. Čtenáři služby Privacy Center message center dostávají e-mailová oznámení včetně těch, která se týkají ochrany osobních údajů, a mohou se odhlásit pomocí předvoleb Centra zpráv. Zprávy o ochraně osobních údajů může číst pouze globální správce a čtečka ochrany osobních údajů centra zpráv. Kromě toho tato role obsahuje možnost zobrazit skupiny, domény a odběry. Tato role nemá oprávnění k zobrazení, vytvoření nebo správě požadavků na služby.

### <a name="message-center-reader"></a>[Čtečka Centra zpráv](#message-center-reader-permissions)

Uživatelé v této roli můžou monitorovat oznámení a aktualizace stavu v [Centru zpráv Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pro svou organizaci na konfigurovaných službách, jako je Exchange, Intune a Microsoft Teams. Čtenáři Centra zpráv dostávají týdenní e-mailové přehledy příspěvků, aktualizací a můžou sdílet příspěvky v Centru zpráv v Office 365. Ve službě Azure AD budou mít uživatelé přiřazení k této roli jenom přístup jen pro čtení ve službách Azure AD, jako jsou uživatelé a skupiny. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="office-apps-administrator"></a>[Správce aplikací Office](#office-apps-administrator-permissions)

Uživatelé v této roli můžou spravovat nastavení cloudu aplikací Office 365. To zahrnuje správu cloudových zásad, samoobslužnou správu stahování a možnost zobrazit sestavy související s aplikacemi Office. Tato role navíc uděluje možnost spravovat lístky podpory a sledovat stav služby v rámci hlavního centra pro správu. Uživatelé přiřazení k této roli mohou také spravovat komunikaci s novými funkcemi v aplikacích Office. 

### <a name="partner-tier1-support"></a>[Podpora partnerské úrovně1](#partner-tier1-support-permissions)

Nepoužívat. Tato role byla zastaralá a bude odebrána z Azure AD v budoucnu. Tato role je určena pro malý počet partnerů společnosti Microsoft pro další prodej a není určena pro obecné použití.

### <a name="partner-tier2-support"></a>[Podpora partnerské úrovně2](#partner-tier2-support-permissions)

Nepoužívat. Tato role byla zastaralá a bude odebrána z Azure AD v budoucnu. Tato role je určena pro malý počet partnerů společnosti Microsoft pro další prodej a není určena pro obecné použití.

### <a name="password-administrator"></a>[Správce hesel](#password-administrator-permissions)

Uživatelé s touto rolí mají omezenou schopnost spravovat hesla. Tato role neuděluje možnost spravovat požadavky na služby nebo sledovat stav služby. Správci hesel mohou resetovat hesla jiných uživatelů, kteří nejsou správci nebo členy pouze následujících rolí:

* Čtečky adresářů
* Pozvaní hosté
* Správce hesel

### <a name="power-bi-administrator"></a>[Správce Power BI](#power-bi-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Power BI, pokud je služba k dispozici, stejně jako možnost spravovat lístky podpory a sledovat stav služby. Další informace na [stránce Principy role správce Power BI](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> V rozhraní Microsoft Graph API a Prostředí Azure AD PowerShell je tato role označena jako "Správce služby Power BI ". Je to "Správce Power BI" na [webu Azure Portal](https://portal.azure.com).

### <a name="power-platform-administrator"></a>[Správce napájecí platformy](#power-platform-administrator-permissions)

Uživatelé v této roli můžete vytvářet a spravovat všechny aspekty prostředí, PowerApps, toky, zásady prevence ztráty dat. Kromě toho uživatelé s touto rolí mají možnost spravovat lístky podpory a sledovat stav služby.

### <a name="privileged-authentication-administrator"></a>[Správce privilegovaného ověřování](#privileged-authentication-administrator-permissions)

Uživatelé s touto rolí mohou nastavit nebo obnovit pověření bez hesla pro všechny uživatele, včetně globálních správců, a mohou aktualizovat hesla pro všechny uživatele. Správci privilegovaného ověřování mohou přinutit uživatele, aby se znovu zaregistrovali proti existujícím pověřením bez hesla (např.

### <a name="privileged-role-administrator"></a>[Správce privilegovaných rolí](#privileged-role-administrator-permissions)

Uživatelé s touto rolí můžete spravovat přiřazení rolí ve službě Azure Active Directory, stejně jako v rámci Azure AD Privilegované Správy identit. Kromě toho tato role umožňuje správu všech aspektů správy privilegovaných identit a administrativních jednotek.

> [!IMPORTANT]
> Tato role uděluje možnost spravovat přiřazení pro všechny role Azure AD, včetně role globálního správce. Tato role nezahrnuje žádné další privilegované schopnosti ve službě Azure AD, jako je vytváření nebo aktualizace uživatelů. Uživatelé přiřazení k této roli však mohou udělit sobě nebo ostatním další oprávnění přiřazením dalších rolí.

### <a name="reports-reader"></a>[Čtečka sestav](#reports-reader-permissions)

Uživatelé s touto rolí můžou zobrazit data sestav využití a řídicí panel sestav v Centru pro správu Microsoftu 365 a v kontextovém balíčku přechodu v Power BI. Kromě toho role poskytuje přístup k protokolování přihlášení a aktivity ve službě Azure AD a data vrácená rozhraní API pro vytváření sestav Microsoft Graph. Uživatel přiřazený k roli Čtečka sestav má přístup pouze k relevantním metrikám využití a přijetí. Nemají žádná oprávnění správce ke konfiguraci nastavení nebo přístupu k centrům pro správu specifických pro daný produkt, jako je Exchange. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="search-administrator"></a>[Správce hledání](#search-administrator-permissions)

Uživatelé v této roli mají plný přístup ke všem funkcím správy vyhledávání Microsoft v Centru pro správu Microsoftu 365. Správci vyhledávání mohou delegovat role Správců vyhledávání a Editoru vyhledávání na uživatele a vytvářet a spravovat obsah, jako jsou záložky, Q&As a umístění. Kromě toho tito uživatelé mohou zobrazit centrum zpráv, sledovat stav služby a vytvářet požadavky na služby.

### <a name="search-editor"></a>[Editor vyhledávání](#search-editor-permissions)

Uživatelé v této roli mohou vytvářet, spravovat a odstraňovat obsah pro vyhledávání Microsoft v Centru pro správu Microsoft u 365, včetně záložek, q&as a umístění.

### <a name="security-administrator"></a>[Správce zabezpečení](#security-administrator-permissions)

Uživatelé s touto rolí mají oprávnění ke správě funkcí souvisejících se zabezpečením v Centru zabezpečení Microsoft 365, Azure Active Directory Identity Protection, Azure Information Protection a Office 365 Security & Compliance Center. Další informace o oprávněních Office 365 jsou dostupné na adrese [Oprávnění v Centru zabezpečení & dodržování předpisů Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

V | Může dělat
--- | ---
[Centrum zabezpečení Microsoft 365](https://protection.office.com) | Sledování zásad souvisejících se zabezpečením ve službách Microsoft 365<br>Správa bezpečnostních hrozeb a výstrah<br>Zobrazení sestav
Centrum ochrany identity | Všechna oprávnění role Čtečka zabezpečení<br>Kromě toho možnost provádět všechny operace Identity Protection Center s výjimkou resetování hesel
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Všechna oprávnění role Čtečka zabezpečení<br>**Nelze** spravovat přiřazení nebo nastavení rolí Azure AD.
[Centrum dodržování předpisů pro zabezpečení & Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad zabezpečení<br>Zobrazení, vyšetřování a reakce na bezpečnostní hrozby<br>Zobrazení sestav
Rozšířená ochrana před internetovými útoky Azure | Sledování a reakce na podezřelou aktivitu zabezpečení
Ochrany ATP v programu Windows Defender a edr | Přiřazení rolí<br>Správa skupin strojů<br>Konfigurace detekce hrozeb koncového bodu a automatické nápravy<br>Zobrazení, prozkoumání a reakce na výstrahy
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazení informací o uživateli, zařízení, registraci, konfiguraci a aplikaci<br>V Intune nelze provádět změny.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Přidání správců, přidání zásad a nastavení, nahrávání protokolů a provádění akcí zásad správného řízení
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Umí zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, upravit zásady zabezpečení, zobrazit výstrahy a doporučení, zavřít výstrahy a doporučení.
[Stav služby Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Zobrazení stavu služeb Office 365

### <a name="security-operator"></a>[Bezpečnostní operátor](#security-operator-permissions)

Uživatelé s touto rolí mohou spravovat výstrahy a mít globální přístup jen pro čtení k funkcím souvisejícím se zabezpečením, včetně všech informací v Centru zabezpečení Microsoft 365, Azure Active Directory, Ochraně identity, Privilegované správě identit a Centru dodržování předpisů & zabezpečení Office 365. Další informace o oprávněních Office 365 jsou dostupné na adrese [Oprávnění v Centru zabezpečení & dodržování předpisů Office 365](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

V | Může dělat
--- | ---
[Centrum zabezpečení Microsoft 365](https://protection.office.com) | Všechna oprávnění role Čtečka zabezpečení<br>Zobrazení, vyšetřování a reakce na výstrahy bezpečnostních hrozeb
Centrum ochrany identity | Všechna oprávnění role Čtečka zabezpečení<br>Kromě toho možnost provádět všechny operace Identity Protection Center s výjimkou resetování hesel
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Všechna oprávnění role Čtečka zabezpečení
[Centrum dodržování předpisů pro zabezpečení & Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Všechna oprávnění role Čtečka zabezpečení<br>Zobrazení, prozkoumání a reakce na výstrahy zabezpečení
Ochrany ATP v programu Windows Defender a edr | Všechna oprávnění role Čtečka zabezpečení<br>Zobrazení, prozkoumání a reakce na výstrahy zabezpečení
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Všechna oprávnění role Čtečka zabezpečení
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Všechna oprávnění role Čtečka zabezpečení
[Stav služby Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Zobrazení stavu služeb Office 365

### <a name="security-reader"></a>[Čtečka zabezpečení](#security-reader-permissions)

Uživatelé s touto rolí mají globální přístup jen pro čtení k funkcím souvisejícím se zabezpečením, včetně všech informací v centru zabezpečení Microsoft 365, Azure Active Directory, ochraně identity, privilegované správě identit, stejně jako možnost číst sestavy přihlášení služby Azure Active Directory a protokoly auditu a v Centru zabezpečení office 365 & Compliance Center. Další informace o oprávněních Office 365 jsou dostupné na adrese [Oprávnění v Centru zabezpečení & dodržování předpisů Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

V | Může dělat
--- | ---
[Centrum zabezpečení Microsoft 365](https://protection.office.com) | Zobrazení zásad souvisejících se zabezpečením ve službách Microsoft 365<br>Zobrazení bezpečnostních hrozeb a výstrah<br>Zobrazení sestav
Centrum ochrany identity | Přečtěte si všechny zprávy o zabezpečení a informace o nastavení pro funkce zabezpečení<br><ul><li>Ochrana proti nevyžádané poště<li>Šifrování<li>Prevence ztráty dat<li>Ochrana proti malwaru<li>Rozšířená ochrana před internetovými útoky<li>Ochrana proti phishingu<li>Pravidla toku pošty
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Má přístup jen pro čtení ke všem informacím, které se objevily v Azure AD Privileged Identity Management: Zásady a sestavy pro přiřazení rolí Azure AD a kontroly zabezpečení.<br>**Nelze** se zaregistrovat pro Azure AD Privileged Identity Management nebo provést žádné změny v něm. Na portálu správy privilegovaných identit nebo prostřednictvím prostředí PowerShell může někdo v této roli aktivovat další role (například globální správce nebo správce privilegovaných rolí), pokud pro ně má uživatel nárok.
[Centrum dodržování předpisů pro zabezpečení & Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zobrazení zásad zabezpečení<br>Zobrazení a vyšetřování bezpečnostních hrozeb<br>Zobrazení sestav
Ochrany ATP v programu Windows Defender a edr | Zobrazit a vyšetřovat výstrahy. Když zapnete řízení přístupu na základě rolí v programu Ochrana ATP v programu Windows Defender, uživatelé s oprávněními jen pro čtení, jako je například role čtečky zabezpečení Azure AD ztratí přístup, dokud nejsou přiřazeny k roli ochrany ATP programu Windows Defender.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Zobrazení informací o uživateli, zařízení, registraci, konfiguraci a aplikaci. Nemůže provádět změny v Intune.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Umí zobrazit doporučení a výstrahy, zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny.
[Stav služby Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Zobrazení stavu služeb Office 365

### <a name="service-support-administrator"></a>[Správce podpory služby](#service-support-administrator-permissions)

Uživatelé s touto rolí můžou otevírat žádosti o podporu se službami Microsoft for Azure a Office 365 a zobrazovat řídicí panel služeb a centrum zpráv na [webu Azure Portal](https://portal.azure.com) a v Centru pro [správu Microsoftu 365](https://admin.microsoft.com). Další informace na [stránce O rolích správců](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Dříve se tato role nazývala "Správce služeb" v [Azure Portal](https://portal.azure.com) a V Centru [pro správu Microsoftu 365](https://admin.microsoft.com). Přejmenovali jsme ho na "Správce podpory služeb", aby se zarovnal s názvem exsiting v rozhraní Microsoft Graph API, rozhraní Azure AD Graph API a Azure AD PowerShell.

### <a name="sharepoint-administrator"></a>[Správce služby SharePoint](#sharepoint-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci služby Microsoft SharePoint Online, pokud je služba k dispozici, stejně jako možnost vytvářet a spravovat všechny skupiny Office 365, spravovat lístky podpory a sledovat stav služby. Další informace na [stránce O rolích správců](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> V rozhraní Microsoft Graph API a prostředí Azure AD PowerShell je tato role označena jako "Správce služby SharePoint Service". Je to "Správce SharePointu" na [webu Azure Portal](https://portal.azure.com).

### <a name="skype-for-business--lync-administrator"></a>[Skype pro firmy / Správce Lyncu](#lync-service-administrator-permissions)

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Skypu pro firmy, když je služba k dispozici, a také spravovat atributy uživatelů specifických pro Skype ve službě Azure Active Directory. Tato role navíc uděluje možnost spravovat lístky podpory a sledovat stav služby a přístup k týmům a Centru pro správu Skypu pro firmy. Účet musí být také licencován pro teams nebo nemůže spouštět rutiny Teams PowerShell. Další informace na stránce [O roli správce Skypu pro firmy](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) a licenčních informací pro Teams na stránce [Licencování doplňků Skypu pro firmy a Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> V rozhraní Microsoft Graph API a Prostředí Azure AD PowerShell je tato role označena jako "Správce služby Lyncu". Je to "Správce Skypu pro firmy" na [webu Azure Portal](https://portal.azure.com/).

### <a name="teams-communications-administrator"></a>[Správce komunikace teams](#teams-communications-administrator-permissions)

Uživatelé v této roli mohou spravovat aspekty úlohy Microsoft Teams související s hlasovou & telefonie. To zahrnuje nástroje pro správu pro přiřazení telefonních čísel, hlasové a zásady schůzky a úplný přístup k sadě nástrojů analýzy hovorů.

### <a name="teams-communications-support-engineer"></a>[Technik podpory komunikace teams](#teams-communications-support-engineer-permissions)

Uživatelé v této roli můžou řešit problémy s komunikací v rámci Microsoft Teams & Skype pro firmy pomocí nástrojů pro řešení potíží s uživatelskými voláními v Centru pro správu Microsoft Teams & Skype pro firmy. Uživatelé v této roli mohou zobrazit úplné informace o záznamu volání pro všechny zúčastněné účastníky. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="teams-communications-support-specialist"></a>[Specialista na podporu komunikace týmů](#teams-communications-support-specialist-permissions)

Uživatelé v této roli můžou řešit problémy s komunikací v rámci Microsoft Teams & Skype pro firmy pomocí nástrojů pro řešení potíží s uživatelskými voláními v Centru pro správu Microsoft Teams & Skype pro firmy. Uživatelé v této roli mohou zobrazit pouze podrobnosti o uživateli ve volání pro konkrétního uživatele, které vyhledávají. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

### <a name="teams-service-administrator"></a>[Správce služby Teams](#teams-service-administrator-permissions)

Uživatelé v této roli můžou spravovat všechny aspekty zatížení Microsoft Teams prostřednictvím Microsoft Teams & Centra pro správu Skypu pro firmy a příslušných modulů PowerShellu. To zahrnuje mimo jiné všechny nástroje pro správu související s telefonií, zasíláním zpráv, schůzkami a samotnými týmy. Tato role navíc uděluje možnost vytvářet a spravovat všechny skupiny Office 365, spravovat lístky podpory a sledovat stav služby.

### <a name="user-administrator"></a>[Správce uživatelů](#user-administrator-permissions)

Uživatelé s touto rolí mohou vytvářet uživatele a spravovat všechny aspekty uživatelů s určitými omezeními (viz níže) a mohou aktualizovat zásady vypršení platnosti hesla. Uživatelé s touto rolí mohou navíc vytvářet a spravovat všechny skupiny. Tato role také zahrnuje možnost vytvářet a spravovat zobrazení uživatelů, spravovat lístky podpory a sledovat stav služby. Správci uživatelů nemají oprávnění ke správě některých uživatelských vlastností pro uživatele ve většině rolí správce. Uživatel s touto rolí nemají oprávnění ke správě vícefaktorové ověřování. Role, které jsou výjimkami z tohoto omezení jsou uvedeny v následující tabulce.

| | |
| --- | --- |
|Obecná oprávnění|<p>Vytváření uživatelů a skupin</p><p>Vytvářet a spravovat zobrazení uživatelů</p><p>Správa lístků podpory Office<p>Aktualizovat zásady vypršení platnosti hesla|
|<p>Na všech uživatelích, včetně všech správců</p>|<p>Správa licencí</p><p>Spravovat všechny vlastnosti uživatele kromě hlavního uživatelského jména</p>
|Pouze u uživatelů, kteří nejsou správci nebo v některé z následujících omezených rolí správce:<ul><li>Čtečky adresářů<li>Pozvaní hosté<li>Správce technické podpory<li>Čtečka Centra zpráv<li>Čtečka sestav<li>Správce uživatelů|<p>Odstranit a obnovit</p><p>Zakázat a povolit</p><p>Zneplatnit obnovovací tokeny</p><p>Spravovat všechny vlastnosti uživatele včetně hlavního uživatelského jména</p><p>Resetování hesla</p><p>Aktualizovat (FIDO) klíče zařízení</p>|

> [!IMPORTANT]
> Uživatelé s touto rolí můžete změnit hesla pro uživatele, kteří mohou mít přístup k citlivým nebo soukromým informacím nebo kritické konfigurace uvnitř i vně služby Azure Active Directory. Změna hesla uživatele může znamenat možnost převzít identitu a oprávnění tohoto uživatele. Příklad:
>
>- Vlastníci aplikací a podnikových aplikací, kteří mohou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace mohou mít privilegovaná oprávnění ve službě Azure AD a jinde, která nejsou udělena správcům uživatelů. Prostřednictvím této cesty může správce uživatele převzít identitu vlastníka aplikace a dále převzít identitu privilegované aplikace aktualizací pověření pro aplikaci.
>- Vlastníci předplatného Azure, kteří mohou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
>- Security Group a Office 365 Group vlastníci, kteří mohou spravovat členství ve skupině. Tyto skupiny mohou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci ve službě Azure AD i jinde.
>- Správci v jiných službách mimo Azure AD, jako je Exchange Online, Office Security and Compliance Center a systémy lidských zdrojů.
>- Nesprávci, jako jsou vedoucí pracovníci, právní zástupci a zaměstnanci v oblasti lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

## <a name="role-permissions"></a>Oprávnění rolí

Následující tabulky popisují konkrétní oprávnění ve službě Azure Active Directory udělená každé roli. Některé role mohou mít další oprávnění ve službách Microsoftu mimo Azure Active Directory.

### <a name="application-administrator-permissions"></a>Oprávnění správce aplikace

Můžete vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/Application/appProxyAuthentication/update | Aktualizujte vlastnosti ověřování proxy serveru aplikace na objektových objektech služby Azure Active Directory. |
| microsoft.directory/Application/appProxyUrlSettings/update | Aktualizujte interní a externí adresy URL proxy aplikace ve službě Azure Active Directory. |
| microsoft.directory/applications/applicationProxy/read | Přečtěte si všechny vlastnosti proxy aplikace. |
| Microsoft.directory/applications/applicationProxy/update | Aktualizujte všechny vlastnosti proxy aplikace. |
| microsoft.directory/applications/audience/update | Aktualizujte vlastnost applications.audience ve službě Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aktualizujte vlastnost applications.authentication ve službě Azure Active Directory. |
| Microsoft.directory/applications/basic/update | Aktualizujte základní vlastnosti aplikací ve službě Azure Active Directory. |
| Microsoft.directory/applications/create | Vytvářejte aplikace ve službě Azure Active Directory. |
| Microsoft.directory/applications/credentials/update | Aktualizujte vlastnost applications.credentials ve službě Azure Active Directory. |
| microsoft.directory/applications/delete | Odstraňte aplikace ve službě Azure Active Directory. |
| soubor microsoft.directory/applications/owners/update | Aktualizujte vlastnost applications.owners ve službě Azure Active Directory. |
| soubor microsoft.directory/applications/permissions/update | Aktualizujte vlastnost applications.permissions ve službě Azure Active Directory. |
| Soubor Microsoft.directory/applications/policies/update | Aktualizujte vlastnost applications.policies ve službě Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Vytvořte appRoleAssignments ve službě Azure Active Directory. |
| microsoft.directory/appRoleAssignments/read | Přečtěte si appRoleAssignments ve službě Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aktualizujte aplikaceRoleAssignments ve službě Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Odstraňte úkoly role aplikace ve službě Azure Active Directory. |
| Microsoft.directory/auditLogs/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs ve službě Azure Active Directory. |
| microsoft.directory/connectorGroups/everything/read | Přečtěte si vlastnosti skupiny konektorů proxy aplikace ve službě Azure Active Directory. |
| Microsoft.directory/connectorGroups/everything/update | Aktualizujte všechny vlastnosti skupiny konektorů proxy aplikací ve službě Azure Active Directory. |
| Microsoft.directory/connectorGroups/create | Vytvořte skupiny konektorů proxy aplikací ve službě Azure Active Directory. |
| Microsoft.directory/connectorGroups/delete | Odstraňte skupiny konektorů proxy aplikací ve službě Azure Active Directory. |
| microsoft.directory/connectors/everything/read | Přečtěte si všechny vlastnosti konektoru proxy aplikace ve službě Azure Active Directory. |
| Microsoft.directory/connectors/create | Vytvořte konektory proxy aplikací ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/basic/read | Přečtěte si vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/basic/update | Aktualizujte vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/create | Vytvořte zásady ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/delete | Odstraňte zásady ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/owners/read | Přečtěte si vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/owners/update | Aktualizujte vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Přečtěte si vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizujte vlastnost servicePrincipals.appRoleAssignedTo ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignments/update | Aktualizujte vlastnost servicePrincipals.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/audience/update | Aktualizujte vlastnost servicePrincipals.audience ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/authentication/update | Aktualizujte vlastnost servicePrincipals.authentication ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/basic/update | Aktualizujte základní vlastnosti na servicePrincipals ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/create | Vytvořte ve službě Azure Active Directory hlavní služby. |
| Microsoft.directory/servicePrincipals/credentials/update | Aktualizujte vlastnost servicePrincipals.credentials ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/delete | Odstraňte servicePrincipals ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/owners/update | Aktualizujte vlastnost servicePrincipals.owners ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/permissions/update | Aktualizujte vlastnost servicePrincipals.permissions ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/policies/update | Aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) ve službě SignInReports ve službě Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="application-developer-permissions"></a>Oprávnění vývojáře aplikací

Může vytvářet registrace aplikací nezávisle na nastavení "Uživatelé mohou registrovat aplikace".

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/applications/createAsOwner | Vytvářejte aplikace ve službě Azure Active Directory. Tvůrce je přidán jako první vlastník a vytvořený objekt se započítá v rámci kvóty 250 vytvořených objektů tvůrce. |
| microsoft.directory/appRoleAssignments/createAsOwner | Vytvořte appRoleAssignments ve službě Azure Active Directory. Tvůrce je přidán jako první vlastník a vytvořený objekt se započítá v rámci kvóty 250 vytvořených objektů tvůrce. |
| Microsoft.directory/oAuth2PermissionGrants/createAsOwner | Vytvořte oAuth2PermissionGrants ve službě Azure Active Directory. Tvůrce je přidán jako první vlastník a vytvořený objekt se započítá v rámci kvóty 250 vytvořených objektů tvůrce. |
| Microsoft.directory/servicePrincipals/createAsOwner | Vytvořte ve službě Azure Active Directory hlavní služby. Tvůrce je přidán jako první vlastník a vytvořený objekt se započítá v rámci kvóty 250 vytvořených objektů tvůrce. |

### <a name="authentication-administrator-permissions"></a>Oprávnění správce ověřování

Povoleno zobrazit, nastavit a obnovit informace o metodě ověřování pro všechny uživatele, kteří nejsou správci.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/users/invalidateAllRefreshTokens | Zneplatnit všechny tokeny aktualizace uživatelů ve službě Azure Active Directory. |
| Microsoft.directory/users/strongAuthentication/update | Aktualizujte silné vlastnosti ověřování, jako jsou informace o pověření mfa. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| soubor microsoft.directory/users/password/update | Aktualizujte hesla pro všechny uživatele v organizaci Office 365. Další podrobnosti najdete v online dokumentaci. |

### <a name="azure-devops-administrator-permissions"></a>Oprávnění správce Azure DevOps

Můžete spravovat zásady a nastavení organizace Azure DevOps.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v [popisu role.](#azure-devops-administrator)
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.devOps/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure DevOps. |

### <a name="azure-information-protection-administrator-permissions"></a>Oprávnění správce ochrany informací Azure

Můžete spravovat všechny aspekty služby Azure Information Protection.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v [popisu role.](#)
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Spravujte všechny aspekty Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="b2c-user-flow-administrator-permissions"></a>Oprávnění správce toku uživatelů B2C

Vytvořte a spravujte všechny aspekty toků uživatelů.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Čtení a konfigurace toků uživatelů ve službě Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>Oprávnění správce atributů toku uživatelů B2C

Vytvořte a spravujte schéma atributů, které je k dispozici pro všechny toky uživatelů.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Čtení a konfigurace uživatelských atributů ve službě Azure Active Directory B2C |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Oprávnění správce sady klíčů B2C IEF

Správa tajných kódů pro federaci a šifrování v rozhraní Identity Experience Framework.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Čtení a konfigurace sad klíčů ve službě Azure Active Directory B2C |

### <a name="b2c-ief-policy-administrator-permissions"></a>Oprávnění správce zásad IEF b2C

Vytvořte a spravujte zásady architektury důvěryhodnosti v rámci prostředí identity.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Přečtěte si a nakonfigurujte vlastní zásady ve službě Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Oprávnění správce fakturace

Může provádět běžné úkoly související s fakturací, jako je aktualizace platebních údajů.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/organization/basic/update | Aktualizujte základní vlastnosti organizace ve službě Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Spravujte všechny aspekty fakturace Office 365. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="cloud-application-administrator-permissions"></a>Oprávnění správce cloudových aplikací

Můžete vytvářet a spravovat všechny aspekty registrace aplikací a podnikových aplikací s výjimkou proxy aplikací.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/applications/audience/update | Aktualizujte vlastnost applications.audience ve službě Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Aktualizujte vlastnost applications.authentication ve službě Azure Active Directory. |
| Microsoft.directory/applications/basic/update | Aktualizujte základní vlastnosti aplikací ve službě Azure Active Directory. |
| Microsoft.directory/applications/create | Vytvářejte aplikace ve službě Azure Active Directory. |
| Microsoft.directory/applications/credentials/update | Aktualizujte vlastnost applications.credentials ve službě Azure Active Directory. |
| microsoft.directory/applications/delete | Odstraňte aplikace ve službě Azure Active Directory. |
| soubor microsoft.directory/applications/owners/update | Aktualizujte vlastnost applications.owners ve službě Azure Active Directory. |
| soubor microsoft.directory/applications/permissions/update | Aktualizujte vlastnost applications.permissions ve službě Azure Active Directory. |
| Soubor Microsoft.directory/applications/policies/update | Aktualizujte vlastnost applications.policies ve službě Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Vytvořte appRoleAssignments ve službě Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aktualizujte aplikaceRoleAssignments ve službě Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Odstraňte úkoly role aplikace ve službě Azure Active Directory. |
| Microsoft.directory/auditLogs/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/create | Vytvořte zásady ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/basic/read | Přečtěte si vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/basic/update | Aktualizujte vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/delete | Odstraňte zásady ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/owners/read | Přečtěte si vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/owners/update | Aktualizujte vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| Microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Přečtěte si vlastnost policies.applicationConfiguration ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizujte vlastnost servicePrincipals.appRoleAssignedTo ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignments/update | Aktualizujte vlastnost servicePrincipals.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/audience/update | Aktualizujte vlastnost servicePrincipals.audience ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/authentication/update | Aktualizujte vlastnost servicePrincipals.authentication ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/basic/update | Aktualizujte základní vlastnosti na servicePrincipals ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/create | Vytvořte ve službě Azure Active Directory hlavní služby. |
| Microsoft.directory/servicePrincipals/credentials/update | Aktualizujte vlastnost servicePrincipals.credentials ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/delete | Odstraňte servicePrincipals ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/owners/update | Aktualizujte vlastnost servicePrincipals.owners ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/permissions/update | Aktualizujte vlastnost servicePrincipals.permissions ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/policies/update | Aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) ve službě SignInReports ve službě Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="cloud-device-administrator-permissions"></a>Oprávnění správce cloudových zařízení

Úplný přístup ke správě zařízení ve službě Azure AD.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/auditLogs/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs ve službě Azure Active Directory. |
| Microsoft.directory/devices/bitLockerRecoveryKeys/read | Přečtěte si vlastnost devices.bitLockerRecoveryKeys ve službě Azure Active Directory. |
| microsoft.directory/devices/delete | Odstraňte zařízení ve službě Azure Active Directory. |
| Microsoft.directory/devices/disable Microsoft.directory/devices/disable Microsoft.directory/devices/disable Microsoft. | Zakažte zařízení ve službě Azure Active Directory. |
| Microsoft.directory/devices/enable | Povolte zařízení ve službě Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) ve službě SignInReports ve službě Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |

### <a name="company-administrator-permissions"></a>Oprávnění správce společnosti

Můžete spravovat všechny aspekty Služby Azure AD a služeb Microsoftu, které používají identity Azure AD. Tato role je také označována jako role globálního správce. 

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Vytvořte a odstraňte všechny prostředky a přečtěte si a aktualizujte standardní vlastnosti v microsoft.aad.cloudAppSecurity. |
| Microsoft.directory/administrativeUnits/allProperties/allTasks | Vytvořte a odstraňte administrativní jednotky a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/applications/allProperties/allTasks | Vytvářejte a odstraňujte aplikace a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | Vytvořte a odstraňte appRoleAssignments a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/auditLogs/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs ve službě Azure Active Directory. |
| Microsoft.directory/contacts/allProperties/allTasks | Vytvořte a odstraňte kontakty a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/contracts/allProperties/allTasks | Vytvořte a odstraňte smlouvy a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/devices/allProperties/allTasks | Vytvořte a odstraňte zařízení a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/directoryRoles/allProperties/allTasks | Vytvořte a odstraňte adresářRole a přečtěte a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Vytvořte a odstraňte adresářRoleTemplates a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/domains/allProperties/allTasks | Vytvořte a odstraňte domény a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/groups/allProperties/allTasks | Vytvořte a odstraňte skupiny a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/groupSettings/allProperties/allTasks | Vytvořte a odstraňte nastavení skupiny a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/groupSettingTemplates/allProperties/allTasks | Vytvořte a odstraňte groupSettingTemplates a přečtěte a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | Vytvořte a odstraňte loginTenantBranding a přečtěte a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Vytvořte a odstraňte oAuth2PermissionGrants a přečtěte a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/organization/allProperties/allTasks | Vytvořte a odstraňte organizaci a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/policies/allProperties/allTasks | Vytvořte a odstraňte zásady a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/roleAssignments/allProperties/allTasks | Vytvořte a odstraňte roleÚlohy a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/roleDefinitions/allProperties/allTasks | Vytvořte a odstraňte definice rolí a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Vytvořte a odstraňte oborRoleMemberships a přečtěte a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| microsoft.directory/serviceAction/activateService | Ve službě Azure Active Directory lze provést akci služby Aktivovat službu. |
| Microsoft.directory/serviceAction/disableDirectoryFeature | Ve službě Azure Active Directory lze provést akci Zakázat funkci adresáře. |
| Microsoft.directory/serviceAction/enableDirectoryFeature | Ve službě Azure Active Directory lze provést akci Enabledirectoryfeature service. |
| Microsoft.directory/serviceAction/getAvailableExtentionProperties | Ve službě Azure Active Directory lze provést akci služby Getavailableextentionproperties. |
| Microsoft.directory/servicePrincipals/allProperties/allTasks | Vytvořte a odstraňte servicePrincipals a přečtěte a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) ve službě SignInReports ve službě Azure Active Directory. |
| Microsoft.directory/subscribedSkus/allProperties/allTasks | Vytvořte a odstraňte subscribedSkus a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/users/allProperties/allTasks | Vytvořte a odstraňte uživatele a přečtěte si a aktualizujte všechny vlastnosti ve službě Azure Active Directory. |
| Microsoft.directorySync/allEntities/allTasks | Proveďte všechny akce ve službě Azure AD Connect. |
| Microsoft.aad.identityProtection/allEntities/allTasks | Vytvořte a odstraňte všechny prostředky a přečtěte si a aktualizujte standardní vlastnosti v aplikaci microsoft.aad.identityProtection. |
| Microsoft.aad.privilegedIdentityManagement/allEntities/read | Přečtěte si všechny prostředky v microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Přečtěte si všechny prostředky v microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Spravujte všechny aspekty Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Spravujte všechny aspekty fakturace Office 365. |
| Microsoft.intune/allEntities/allTasks | Spravujte všechny aspekty Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Správa všech aspektů Správce dodržování předpisů Office 365 |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Spravujte všechny aspekty desktopové analýzy. |
| microsoft.office365.exchange/allEntities/allTasks | Spravujte všechny aspekty Exchange Online. |
| Microsoft.office365.lockbox/allEntities/allTasks | Správa všech aspektů bezpečnostního schránky zákazníků Office 365 |
| microsoft.office365.messageCentrum/zprávy/čtení | Čtení zpráv v aplikaci microsoft.office365.messageCenter |
| microsoft.office365.messageCentrum/securityMessages/read | Přečtěte si zabezpečeníZprávy v microsoft.office365.messageCenter. |
| microsoft.office365.protectionCentrum/allEntities/allTasks | Spravujte všechny aspekty Centra ochrany Office 365. |
| Microsoft.office365.securityComplianceCenter/allEntities/allTasks | Vytvořte a odstraňte všechny prostředky a přečtěte si a aktualizujte standardní vlastnosti v centru Microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Vytvořte a odstraňte všechny prostředky a přečtěte si a aktualizujte standardní vlastnosti v aplikaci Microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Spravujte všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy využití Office 365. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Spravujte všechny aspekty aplikace Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Spravujte všechny aspekty Power BI. |
| Microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Přečtěte si všechny prostředky v souboru Microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Oprávnění správce dodržování předpisů

Můžete číst a spravovat konfigurace dodržování předpisů a sestavy ve službě Azure AD a Office 365.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Správa všech aspektů Správce dodržování předpisů Office 365 |
| microsoft.office365.exchange/allEntities/allTasks | Spravujte všechny aspekty Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Vytvořte a odstraňte všechny prostředky a přečtěte si a aktualizujte standardní vlastnosti v aplikaci Microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Spravujte všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="compliance-data-administrator-permissions"></a>Oprávnění správce dat dodržování předpisů

Vytváří a spravuje obsah dodržování předpisů.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Přečtěte si a nakonfigurujte Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Spravujte všechny aspekty Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Správa všech aspektů Správce dodržování předpisů Office 365 |
| microsoft.office365.exchange/allEntities/allTasks | Spravujte všechny aspekty Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Vytvořte a odstraňte všechny prostředky a přečtěte si a aktualizujte standardní vlastnosti v aplikaci Microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Spravujte všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="conditional-access-administrator-permissions"></a>Oprávnění správce podmíněného přístupu

Může spravovat funkce podmíněného přístupu.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | Přečtěte si vlastnost policies.conditionalAccess ve službě Azure Active Directory. |
| Microsoft.directory/policies/conditionalAccess/basic/update | Aktualizujte vlastnost policies.conditionalAccess ve službě Azure Active Directory. |
| Microsoft.directory/policies/conditionalAccess/create | Vytvořte zásady ve službě Azure Active Directory. |
| Microsoft.directory/policies/conditionalAccess/delete | Odstraňte zásady ve službě Azure Active Directory. |
| Microsoft.directory/policies/conditionalAccess/owners/read | Přečtěte si vlastnost policies.conditionalAccess ve službě Azure Active Directory. |
| Microsoft.directory/policies/conditionalAccess/owners/update | Aktualizujte vlastnost policies.conditionalAccess ve službě Azure Active Directory. |
| Microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | Přečtěte si vlastnost policies.conditionalAccess ve službě Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | Aktualizujte vlastnost policies.conditionalAccess ve službě Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Oprávnění správce služby CRM

Dokáže spravovat všechny aspekty produktu Dynamics 365.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Spravujte všechny aspekty aplikace Dynamics 365. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="customer-lockbox-access-approver-permissions"></a>Oprávnění schvalovatele přístupu k blokování zákazníků

Může schvalovat žádosti o podporu společnosti Microsoft pro přístup k datům organizace zákazníků.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| Microsoft.office365.lockbox/allEntities/allTasks | Správa všech aspektů bezpečnostního schránky zákazníků Office 365 |

### <a name="desktop-analytics-administrator-permissions"></a>Oprávnění správce služby Desktop Analytics

Můžete spravovat služby zásad & desktopové analýzy a office. V případě desktopové analýzy to zahrnuje možnost zobrazit inventář datových zdrojů, vytvořit plány nasazení, zobrazit nasazení a stav stavu. U služby & zásad vlastního nastavení sady Office tato role umožňuje uživatelům spravovat zásady sady Office.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Spravujte všechny aspekty desktopové analýzy. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="device-administrators-permissions"></a>Oprávnění správců zařízení

Uživatelé přiřazení k této roli jsou přidáni do skupiny místních správců na zařízeních spojených s Azure AD.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/groupSettings/basic/read | Přečtěte si základní vlastnosti ve skupiněNastavení ve službě Azure Active Directory. |
| Microsoft.directory/groupSettingTemplates/basic/read | Přečtěte si základní vlastnosti na groupSettingTemplates ve službě Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Oprávnění čtenáři adresářů
Umí číst základní informace o adresáři. Pro udělení přístupu k aplikacím, které nejsou určeny pro uživatele.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/administrativeUnits/basic/read | Přečtěte si základní vlastnosti na jednotkách pro správu ve službě Azure Active Directory. |
| Microsoft.directory/administrativeUnits/members/read | Přečtěte si vlastnost administrativeUnits.members ve službě Azure Active Directory. |
| Microsoft.directory/applications/basic/read | Přečtěte si základní vlastnosti aplikací ve službě Azure Active Directory. |
| microsoft.directory/applications/owners/read | Ve službě Azure Active Directory si můžete přečíst vlastnost applications.owners. |
| Microsoft.directory/applications/policies/read | Přečtěte si vlastnost applications.policies ve službě Azure Active Directory. |
| microsoft.directory/contacts/basic/read | Přečtěte si základní vlastnosti kontaktů ve službě Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read | Čtení contacts.memberOf vlastnosti ve službě Azure Active Directory. |
| microsoft.directory/contracts/basic/read | Přečtěte si základní vlastnosti smluv ve službě Azure Active Directory. |
| microsoft.directory/devices/basic/read | Přečtěte si základní vlastnosti na zařízeních ve službě Azure Active Directory. |
| microsoft.directory/devices/memberOf/read | Čtení vlastností devices.memberOf ve službě Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read | Ve službě Azure Active Directory si můžete přečíst vlastnost devices.registeredOwners. |
| Microsoft.directory/devices/registeredUsers/read | Čtení vlastností devices.registeredUsers ve službě Azure Active Directory. |
| Microsoft.directory/directoryRoles/basic/read | Přečtěte si základní vlastnosti v adresářiRole ve službě Azure Active Directory. |
| Microsoft.directory/directoryRoles/eligibleMembers/read | Přečtěte si vlastnost directoryRoles.eligibleMembers ve službě Azure Active Directory. |
| Microsoft.directory/directoryRoles/members/read | Přečtěte si vlastnost directoryRoles.members ve službě Azure Active Directory. |
| microsoft.directory/domains/basic/read | Přečtěte si základní vlastnosti domén ve službě Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/read | Přečtěte si vlastnost groups.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/groups/basic/read | Přečtěte si základní vlastnosti skupin ve službě Azure Active Directory. |
| microsoft.directory/groups/memberOf/read | Čtení groups.memberOf vlastnosti ve službě Azure Active Directory. |
| microsoft.directory/groups/members/read | Ve službě Azure Active Directory si přečtěte vlastnost groups.members. |
| microsoft.directory/groups/owners/read | Ve službě Azure Active Directory si přečtěte vlastnost groups.owners. |
| Microsoft.directory/groups/settings/read | Ve službě Azure Active Directory si přečtěte vlastnost groups.settings. |
| Microsoft.directory/groupSettings/basic/read | Přečtěte si základní vlastnosti ve skupiněNastavení ve službě Azure Active Directory. |
| Microsoft.directory/groupSettingTemplates/basic/read | Přečtěte si základní vlastnosti na groupSettingTemplates ve službě Azure Active Directory. |
| Microsoft.directory/oAuth2PermissionGrants/basic/read | Přečtěte si základní vlastnosti na oAuth2PermissionGrants ve službě Azure Active Directory. |
| microsoft.directory/organization/basic/read | Přečtěte si základní vlastnosti organizace ve službě Azure Active Directory. |
| Microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Přečtěte si vlastnost organization.trustedCAsForPasswordlessAuth ve službě Azure Active Directory. |
| Microsoft.directory/roleAssignments/basic/read | Přečtěte si základní vlastnosti roleÚkoly ve službě Azure Active Directory. |
| Microsoft.directory/roleDefinitions/basic/read | Přečtěte si základní vlastnosti definic rolí ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Přečtěte si vlastnost servicePrincipals.appRoleAssignedTo ve službě Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Přečtěte si vlastnost servicePrincipals.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/basic/read | Přečtěte si základní vlastnosti na servicePrincipals ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/memberOf/read | Přečtěte si servicePrincipals.memberOf vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Přečtěte si vlastnost servicePrincipals.oAuth2PermissionGrants ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/ownedObjects/read | Přečtěte si vlastnost servicePrincipals.ownedObjects ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/owners/read | Přečtěte si vlastnost servicePrincipals.owners ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/policies/read | Přečtěte si vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read | Přečtěte si základní vlastnosti na odebíranéSku ve službě Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read | Přečtěte si vlastnost users.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/users/basic/read | Přečtěte si základní vlastnosti uživatelů ve službě Azure Active Directory. |
| microsoft.directory/users/directReports/read | Číst vlastnost users.directReports ve službě Azure Active Directory. |
| microsoft.directory/users/manager/read | Přečtěte si vlastnost users.manager ve službě Azure Active Directory. |
| microsoft.directory/users/memberOf/read | Čtení users.memberOf vlastnost i ve službě Azure Active Directory. |
| Microsoft.directory/users/oAuth2PermissionGrants/basic/read | Přečtěte si vlastnost users.oAuth2PermissionGrants ve službě Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Ve službě Azure Active Directory si můžete přečíst vlastnost users.ownedDevices. |
| Microsoft.directory/users/ownedObjects/read | Ve službě Azure Active Directory si můžete přečíst vlastnost users.ownedObjects. |
| microsoft.directory/users/registeredDevices/read | Přečtěte si vlastnost users.registeredDevices ve službě Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Oprávnění účtů synchronizace adresářů

Používá se pouze službou Azure AD Connect.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/organization/dirSync/update | Aktualizujte vlastnost organization.dirSync ve službě Azure Active Directory. |
| Microsoft.directory/policies/create | Vytvořte zásady ve službě Azure Active Directory. |
| Microsoft.directory/policies/delete | Odstraňte zásady ve službě Azure Active Directory. |
| Microsoft.directory/policies/basic/read | Přečtěte si základní vlastnosti zásad ve službě Azure Active Directory. |
| Soubor Microsoft.directory/policies/basic/update | Aktualizujte základní vlastnosti zásad ve službě Azure Active Directory. |
| Microsoft.directory/policies/owners/read | Přečtěte si vlastnost policies.owners ve službě Azure Active Directory. |
| Soubor Microsoft.directory/policies/owners/update | Aktualizujte vlastnost policies.owners ve službě Azure Active Directory. |
| Microsoft.directory/policies/policiesAppliedTo/read | Přečtěte si vlastnost policies.policiesAppliedTo ve službě Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Aktualizujte vlastnost policies.tenantDefault ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Přečtěte si vlastnost servicePrincipals.appRoleAssignedTo ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Aktualizujte vlastnost servicePrincipals.appRoleAssignedTo ve službě Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Přečtěte si vlastnost servicePrincipals.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignments/update | Aktualizujte vlastnost servicePrincipals.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/audience/update | Aktualizujte vlastnost servicePrincipals.audience ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/authentication/update | Aktualizujte vlastnost servicePrincipals.authentication ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/basic/read | Přečtěte si základní vlastnosti na servicePrincipals ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/basic/update | Aktualizujte základní vlastnosti na servicePrincipals ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/create | Vytvořte ve službě Azure Active Directory hlavní služby. |
| Microsoft.directory/servicePrincipals/credentials/update | Aktualizujte vlastnost servicePrincipals.credentials ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/memberOf/read | Přečtěte si servicePrincipals.memberOf vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Přečtěte si vlastnost servicePrincipals.oAuth2PermissionGrants ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/owners/read | Přečtěte si vlastnost servicePrincipals.owners ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/owners/update | Aktualizujte vlastnost servicePrincipals.owners ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/ownedObjects/read | Přečtěte si vlastnost servicePrincipals.ownedObjects ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/permissions/update | Aktualizujte vlastnost servicePrincipals.permissions ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/policies/read | Přečtěte si vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/policies/update | Aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| Microsoft.directorySync/allEntities/allTasks | Proveďte všechny akce ve službě Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Oprávnění zapisovače adresářů

Umí číst & psát základní informace o adresáři. Pro udělení přístupu k aplikacím, které nejsou určeny pro uživatele.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/groups/create | Vytvořte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/createAsOwner | Vytvořte skupiny ve službě Azure Active Directory. Tvůrce je přidán jako první vlastník a vytvořený objekt se započítá v rámci kvóty 250 vytvořených objektů tvůrce. |
| microsoft.directory/groups/appRoleAssignments/update | Aktualizujte vlastnost groups.appRoleAssignments ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/basic/update | Aktualizujte základní vlastnosti skupin ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/members/update | Aktualizujte vlastnost groups.members ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/owners/update | Aktualizujte vlastnost groups.owners ve službě Azure Active Directory. |
| Microsoft.directory/groups/settings/update | Aktualizujte vlastnost groups.settings ve službě Azure Active Directory. |
| Microsoft.directory/groupSettings/basic/update | Aktualizujte základní vlastnosti ve skupiněNastavení ve službě Azure Active Directory. |
| Microsoft.directory/groupSettings/create | Vytvořte nastavení skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groupSettings/delete | Odstraňte nastavení skupiny ve službě Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aktualizujte vlastnost users.appRoleAssignments ve službě Azure Active Directory. |
| microsoft.directory/users/assignLicense | Správa licencí pro uživatele ve službě Azure Active Directory |
| Microsoft.directory/users/basic/update | Aktualizujte základní vlastnosti uživatelů ve službě Azure Active Directory. |
| Microsoft.directory/users/invalidateAllRefreshTokens | Zneplatnit všechny tokeny aktualizace uživatelů ve službě Azure Active Directory. |
| microsoft.directory/users/manager/update | Aktualizujte vlastnost users.manager ve službě Azure Active Directory. |
| Microsoft.directory/users/userPrincipalName/update | Aktualizujte vlastnost users.userPrincipalName ve službě Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Oprávnění správce služby Exchange Service

Může spravovat všechny aspekty produktu Exchange.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aktualizujte vlastnost groups.unified ve službě Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aktualizujte základní vlastnosti skupin Office 365. |
| microsoft.directory/groups/unified/create | Vytvořte skupiny Office 365. |
| soubor microsoft.directory/groups/unified/delete | Odstranění skupin Office 365 |
| Microsoft.directory/groups/unified/members/update | Aktualizujte členství ve skupinách Office 365. |
| Microsoft.directory/groups/unified/owners/update Microsoft.directory/groups/Unified/owners/update Microsoft.directory/groups/Unified/owners/update Microsoft. | Aktualizujte vlastnictví skupin Office 365. |
| microsoft.office365.exchange/allEntities/allTasks | Spravujte všechny aspekty Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy využití Office 365. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |

### <a name="external-identity-provider-administrator-permissions"></a>Oprávnění správce externího zprostředkovatele identity

Nakonfigurujte zprostředkovatele identit pro použití v přímé federaci.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Čtení a konfigurace poskytovatelů identit ve službě Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Oprávnění globální čtečky
Dokáže přečíst vše, co globální správce může, ale nic neupravuje.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v [popisu role.](#global-reader)
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.commerce.billing/allEntities/read    | Přečtěte si všechny aspekty fakturace Office 365. |
| Microsoft.directory/administrativeUnits/basic/read    | Přečtěte si základní vlastnosti na jednotkách pro správu ve službě Azure Active Directory. |
| Microsoft.directory/administrativeUnits/members/read    | Přečtěte si vlastnost administrativeUnits.members ve službě Azure Active Directory. |
| Microsoft.directory/applications/basic/read    | Přečtěte si základní vlastnosti aplikací ve službě Azure Active Directory. |
| microsoft.directory/applications/owners/read    | Ve službě Azure Active Directory si můžete přečíst vlastnost applications.owners. |
| Microsoft.directory/applications/policies/read    | Přečtěte si vlastnost applications.policies ve službě Azure Active Directory. |
| microsoft.directory/contacts/basic/read    | Přečtěte si základní vlastnosti kontaktů ve službě Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read    | Čtení contacts.memberOf vlastnosti ve službě Azure Active Directory. |
| microsoft.directory/contracts/basic/read    | Přečtěte si základní vlastnosti smluv ve službě Azure Active Directory. |
| microsoft.directory/devices/basic/read    | Přečtěte si základní vlastnosti na zařízeních ve službě Azure Active Directory. |
| microsoft.directory/devices/memberOf/read    | Čtení vlastností devices.memberOf ve službě Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read    | Ve službě Azure Active Directory si můžete přečíst vlastnost devices.registeredOwners. |
| Microsoft.directory/devices/registeredUsers/read    | Čtení vlastností devices.registeredUsers ve službě Azure Active Directory. |
| Microsoft.directory/directoryRoles/basic/read    | Přečtěte si základní vlastnosti v adresářiRole ve službě Azure Active Directory. |
| Microsoft.directory/directoryRoles/eligibleMembers/read    | Přečtěte si vlastnost directoryRoles.eligibleMembers ve službě Azure Active Directory. |
| Microsoft.directory/directoryRoles/members/read    | Přečtěte si vlastnost directoryRoles.members ve službě Azure Active Directory. |
| microsoft.directory/domains/basic/read    | Přečtěte si základní vlastnosti domén ve službě Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/read    | Přečtěte si vlastnost groups.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/groups/basic/read    | Přečtěte si základní vlastnosti skupin ve službě Azure Active Directory. |
| Microsoft.directory/groups/hiddenMembers/read    | Ve službě Azure Active Directory si přečtěte vlastnost groups.hiddenMembers. |
| microsoft.directory/groups/memberOf/read    | Čtení groups.memberOf vlastnosti ve službě Azure Active Directory. |
| microsoft.directory/groups/members/read    | Ve službě Azure Active Directory si přečtěte vlastnost groups.members. |
| microsoft.directory/groups/owners/read    | Ve službě Azure Active Directory si přečtěte vlastnost groups.owners. |
| Microsoft.directory/groups/settings/read    | Ve službě Azure Active Directory si přečtěte vlastnost groups.settings. |
| Microsoft.directory/groupSettings/basic/read    | Přečtěte si základní vlastnosti ve skupiněNastavení ve službě Azure Active Directory. |
| Microsoft.directory/groupSettingTemplates/basic/read    | Přečtěte si základní vlastnosti na groupSettingTemplates ve službě Azure Active Directory. |
| Microsoft.directory/oAuth2PermissionGrants/basic/read    | Přečtěte si základní vlastnosti na oAuth2PermissionGrants ve službě Azure Active Directory. |
| microsoft.directory/organization/basic/read    | Přečtěte si základní vlastnosti organizace ve službě Azure Active Directory. |
| Microsoft.directory/organization/trustedCAsForPasswordlessAuth/read    | Přečtěte si vlastnost organization.trustedCAsForPasswordlessAuth ve službě Azure Active Directory. |
| Microsoft.directory/policies/standard/read    | Přečtěte si standardní zásady ve službě Azure Active Directory. |
| Microsoft.directory/roleAssignments/basic/read    | Přečtěte si základní vlastnosti roleÚkoly ve službě Azure Active Directory. |
| Microsoft.directory/roleDefinitions/basic/read    | Přečtěte si základní vlastnosti definic rolí ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | Přečtěte si vlastnost servicePrincipals.appRoleAssignedTo ve službě Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read    | Přečtěte si vlastnost servicePrincipals.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/basic/read    | Přečtěte si základní vlastnosti na servicePrincipals ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/memberOf/read    | Přečtěte si servicePrincipals.memberOf vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | Přečtěte si vlastnost servicePrincipals.oAuth2PermissionGrants ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/ownedObjects/read    | Přečtěte si vlastnost servicePrincipals.ownedObjects ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/owners/read    | Přečtěte si vlastnost servicePrincipals.owners ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/policies/read    | Přečtěte si vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read    | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) ve službě SignInReports ve službě Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read    | Přečtěte si základní vlastnosti na odebíranéSku ve službě Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read    | Přečtěte si vlastnost users.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/users/basic/read    | Přečtěte si základní vlastnosti uživatelů ve službě Azure Active Directory. |
| microsoft.directory/users/directReports/read    | Číst vlastnost users.directReports ve službě Azure Active Directory. |
| microsoft.directory/users/manager/read    | Přečtěte si vlastnost users.manager ve službě Azure Active Directory. |
| microsoft.directory/users/memberOf/read    | Čtení users.memberOf vlastnost i ve službě Azure Active Directory. |
| Microsoft.directory/users/oAuth2PermissionGrants/basic/read    | Přečtěte si vlastnost users.oAuth2PermissionGrants ve službě Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read    | Ve službě Azure Active Directory si můžete přečíst vlastnost users.ownedDevices. |
| Microsoft.directory/users/ownedObjects/read    | Ve službě Azure Active Directory si můžete přečíst vlastnost users.ownedObjects. |
| microsoft.directory/users/registeredDevices/read    | Přečtěte si vlastnost users.registeredDevices ve službě Azure Active Directory. |
| Microsoft.directory/users/strongAuthentication/read    | Přečtěte si silné vlastnosti ověřování, jako jsou informace o pověření mfa. |
| microsoft.office365.exchange/allEntities/read    | Přečtěte si všechny aspekty Exchange Online. |
| microsoft.office365.messageCentrum/zprávy/čtení    | Čtení zpráv v aplikaci microsoft.office365.messageCenter |
| microsoft.office365.messageCentrum/securityMessages/read    | Přečtěte si zabezpečeníZprávy v microsoft.office365.messageCenter. |
| microsoft.office365.protectionCentrum/allEntities/číst    | Přečtěte si všechny aspekty Centra ochrany Office 365. |
| Microsoft.office365.securityComplianceCenter/allEntities/read    | Přečtěte si všechny standardní vlastnosti v microsoft.office365.securityComplianceCenter. |
| microsoft.office365.usageReports/allEntities/read    | Přečtěte si sestavy využití Office 365. |
| Microsoft.office365.webPortal/allEntities/standard/read    | Přečtěte si standardní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |

### <a name="groups-administrator-permissions"></a>Oprávnění správce skupin
Může spravovat všechny aspekty skupin a nastavení skupin, jako je pojmenování a vypršení platnosti zásady.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/groups/basic/read | Přečtěte si standardní vlastnosti ve skupinách ve službě Azure Active Directory.  |
| soubor microsoft.directory/groups/basic/update | Aktualizujte základní vlastnosti skupin ve službě Azure Active Directory. |
| Microsoft.directory/groups/create | Vytvořte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/createAsOwner | Vytvořte skupiny ve službě Azure Active Directory. Tvůrce je přidán jako první vlastník a vytvořený objekt se započítá v rámci kvóty 250 vytvořených objektů tvůrce. |
| Microsoft.directory/groups/delete | Odstraňte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/hiddenMembers/read | Ve službě Azure Active Directory si přečtěte vlastnost groups.hiddenMembers. |
| soubor microsoft.directory/groups/members/update | Aktualizujte vlastnost groups.members ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/owners/update | Aktualizujte vlastnost groups.owners ve službě Azure Active Directory. |
| Microsoft.directory/groups/restore | Obnovte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/settings/update | Aktualizujte vlastnost groups.settings ve službě Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.office365.messageCentrum/zprávy/čtení | Čtení zpráv v aplikaci microsoft.office365.messageCenter |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="guest-inviter-permissions"></a>Oprávnění pozvaných hostů
Může pozvat uživatele typu Host nezávisle na nastavení "členové mohou pozvat hosty".

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | Přečtěte si vlastnost users.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/users/basic/read | Přečtěte si základní vlastnosti uživatelů ve službě Azure Active Directory. |
| microsoft.directory/users/directReports/read | Číst vlastnost users.directReports ve službě Azure Active Directory. |
| microsoft.directory/users/inviteGuest | Pozvěte uživatele typu Host ve službě Azure Active Directory. |
| microsoft.directory/users/manager/read | Přečtěte si vlastnost users.manager ve službě Azure Active Directory. |
| microsoft.directory/users/memberOf/read | Čtení users.memberOf vlastnost i ve službě Azure Active Directory. |
| Microsoft.directory/users/oAuth2PermissionGrants/basic/read | Přečtěte si vlastnost users.oAuth2PermissionGrants ve službě Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Ve službě Azure Active Directory si můžete přečíst vlastnost users.ownedDevices. |
| Microsoft.directory/users/ownedObjects/read | Ve službě Azure Active Directory si můžete přečíst vlastnost users.ownedObjects. |
| microsoft.directory/users/registeredDevices/read | Přečtěte si vlastnost users.registeredDevices ve službě Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Oprávnění správce technické podpory

Může resetovat hesla pro jiné než správce a správce technické podpory.

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/devices/bitLockerRecoveryKeys/read | Přečtěte si vlastnost devices.bitLockerRecoveryKeys ve službě Azure Active Directory. |
| Microsoft.directory/users/invalidateAllRefreshTokens | Zneplatnit všechny tokeny aktualizace uživatelů ve službě Azure Active Directory. |
| soubor microsoft.directory/users/password/update | Aktualizujte hesla pro všechny uživatele služby Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="intune-service-administrator-permissions"></a>Oprávnění správce služby Intune

Můžete spravovat všechny aspekty produktu Intune.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/contacts/basic/update | Aktualizujte základní vlastnosti kontaktů ve službě Azure Active Directory. |
| microsoft.directory/contacts/create | Vytvořte kontakty ve službě Azure Active Directory. |
| Microsoft.directory/contacts/delete | Odstraňte kontakty ve službě Azure Active Directory. |
| Microsoft.directory/devices/basic/update | Aktualizujte základní vlastnosti na zařízeních ve službě Azure Active Directory. |
| Microsoft.directory/devices/bitLockerRecoveryKeys/read | Přečtěte si vlastnost devices.bitLockerRecoveryKeys ve službě Azure Active Directory. |
| microsoft.directory/devices/create | Vytvářejte zařízení ve službě Azure Active Directory. |
| microsoft.directory/devices/delete | Odstraňte zařízení ve službě Azure Active Directory. |
| Microsoft.directory/devices/registeredOwners/update | Aktualizujte vlastnost devices.registeredOwners ve službě Azure Active Directory. |
| Microsoft.directory/devices/registeredUsers/update | Aktualizujte vlastnost devices.registeredUsers ve službě Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Aktualizujte vlastnost groups.appRoleAssignments ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/basic/update | Aktualizujte základní vlastnosti skupin ve službě Azure Active Directory. |
| Microsoft.directory/groups/create | Vytvořte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/createAsOwner | Vytvořte skupiny ve službě Azure Active Directory. Tvůrce je přidán jako první vlastník a vytvořený objekt se započítá v rámci kvóty 250 vytvořených objektů tvůrce. |
| Microsoft.directory/groups/delete | Odstraňte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/hiddenMembers/read | Ve službě Azure Active Directory si přečtěte vlastnost groups.hiddenMembers. |
| soubor microsoft.directory/groups/members/update | Aktualizujte vlastnost groups.members ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/owners/update | Aktualizujte vlastnost groups.owners ve službě Azure Active Directory. |
| Microsoft.directory/groups/restore | Obnovte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/settings/update | Aktualizujte vlastnost groups.settings ve službě Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aktualizujte vlastnost users.appRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/users/basic/update | Aktualizujte základní vlastnosti uživatelů ve službě Azure Active Directory. |
| microsoft.directory/users/manager/update | Aktualizujte vlastnost users.manager ve službě Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.intune/allEntities/allTasks | Spravujte všechny aspekty Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |

### <a name="kaizala-administrator-permissions"></a>Oprávnění správce Kaizala

Můžete spravovat nastavení pro Microsoft Kaizala.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si Centrum pro správu Office 365. |

### <a name="license-administrator-permissions"></a>Oprávnění správce licencí

Může spravovat licence produktů pro uživatele a skupiny.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/users/assignLicense | Správa licencí pro uživatele ve službě Azure Active Directory |
| Microsoft.directory/users/usageLocation/update | Aktualizujte vlastnost users.usageLocation ve službě Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |

### <a name="lync-service-administrator-permissions"></a>Oprávnění správce služby Lync

Můžete spravovat všechny aspekty produktu Skype pro firmy.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Spravujte všechny aspekty Online Skypu pro firmy. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read    | Přečtěte si sestavy využití Office 365. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |


### <a name="message-center-privacy-reader-permissions"></a>Oprávnění čtečky ochrany osobních údajů Centra zpráv

Umí číst příspěvky Centra zpráv, zprávy o ochraně osobních údajů, skupiny, domény a odběry.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.messageCentrum/zprávy/čtení | Čtení zpráv v aplikaci microsoft.office365.messageCenter |
| microsoft.office365.messageCentrum/securityMessages/read | Přečtěte si zabezpečeníZprávy v microsoft.office365.messageCenter. |

### <a name="message-center-reader-permissions"></a>Oprávnění čtečky Centra zpráv
Můžou číst zprávy a aktualizace pro svou organizaci jenom v Centru zpráv Office 365. 

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.messageCentrum/zprávy/čtení | Čtení zpráv v aplikaci microsoft.office365.messageCenter |

### <a name="office-apps-administrator-permissions"></a>Oprávnění správce aplikací Office Apps
Můžete spravovat cloudové služby aplikací Office, včetně správy zásad a nastavení, a spravovat možnost výběru, zrušení výběru a publikování obsahu funkcí "co je nového" na zařízeních koncových uživatelů.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.office365.messageCentrum/zprávy/čtení | Čtení zpráv v aplikaci microsoft.office365.messageCenter |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| microsoft.office365.userKomunikace/všechny entity/všechny úkoly | Přečtěte si a aktualizujte viditelnost zpráv What's New. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |

### <a name="partner-tier1-support-permissions"></a>Oprávnění podpory Tier1 partnera

Nepoužívejte - není určen pro všeobecné použití.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/contacts/basic/update | Aktualizujte základní vlastnosti kontaktů ve službě Azure Active Directory. |
| microsoft.directory/contacts/create | Vytvořte kontakty ve službě Azure Active Directory. |
| Microsoft.directory/contacts/delete | Odstraňte kontakty ve službě Azure Active Directory. |
| Microsoft.directory/groups/create | Vytvořte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/createAsOwner | Vytvořte skupiny ve službě Azure Active Directory. Tvůrce je přidán jako první vlastník a vytvořený objekt se započítá v rámci kvóty 250 vytvořených objektů tvůrce. |
| soubor microsoft.directory/groups/members/update | Aktualizujte vlastnost groups.members ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/owners/update | Aktualizujte vlastnost groups.owners ve službě Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aktualizujte vlastnost users.appRoleAssignments ve službě Azure Active Directory. |
| microsoft.directory/users/assignLicense | Správa licencí pro uživatele ve službě Azure Active Directory |
| Microsoft.directory/users/basic/update | Aktualizujte základní vlastnosti uživatelů ve službě Azure Active Directory. |
| microsoft.directory/users/delete | Odstraňte uživatele ve službě Azure Active Directory. |
| Microsoft.directory/users/invalidateAllRefreshTokens | Zneplatnit všechny tokeny aktualizace uživatelů ve službě Azure Active Directory. |
| microsoft.directory/users/manager/update | Aktualizujte vlastnost users.manager ve službě Azure Active Directory. |
| soubor microsoft.directory/users/password/update | Aktualizujte hesla pro všechny uživatele služby Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| Microsoft.directory/users/restore | Obnovení odstraněných uživatelů ve službě Azure Active Directory |
| Microsoft.directory/users/userPrincipalName/update | Aktualizujte vlastnost users.userPrincipalName ve službě Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="partner-tier2-support-permissions"></a>Oprávnění podpory Tier2 partnera

Nepoužívejte - není určen pro všeobecné použití.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/contacts/basic/update | Aktualizujte základní vlastnosti kontaktů ve službě Azure Active Directory. |
| microsoft.directory/contacts/create | Vytvořte kontakty ve službě Azure Active Directory. |
| Microsoft.directory/contacts/delete | Odstraňte kontakty ve službě Azure Active Directory. |
| microsoft.directory/domains/allTasks | Vytvářejte a odstraňujte domény a čtěte a aktualizujte standardní vlastnosti ve službě Azure Active Directory. |
| Microsoft.directory/groups/create | Vytvořte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/delete | Odstraňte skupiny ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/members/update | Aktualizujte vlastnost groups.members ve službě Azure Active Directory. |
| Microsoft.directory/groups/restore | Obnovte skupiny ve službě Azure Active Directory. |
| microsoft.directory/organization/basic/update | Aktualizujte základní vlastnosti organizace ve službě Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aktualizujte vlastnost users.appRoleAssignments ve službě Azure Active Directory. |
| microsoft.directory/users/assignLicense | Správa licencí pro uživatele ve službě Azure Active Directory |
| Microsoft.directory/users/basic/update | Aktualizujte základní vlastnosti uživatelů ve službě Azure Active Directory. |
| microsoft.directory/users/delete | Odstraňte uživatele ve službě Azure Active Directory. |
| Microsoft.directory/users/invalidateAllRefreshTokens | Zneplatnit všechny tokeny aktualizace uživatelů ve službě Azure Active Directory. |
| microsoft.directory/users/manager/update | Aktualizujte vlastnost users.manager ve službě Azure Active Directory. |
| soubor microsoft.directory/users/password/update | Aktualizujte hesla pro všechny uživatele služby Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| Microsoft.directory/users/restore | Obnovení odstraněných uživatelů ve službě Azure Active Directory |
| Microsoft.directory/users/userPrincipalName/update | Aktualizujte vlastnost users.userPrincipalName ve službě Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="password-administrator-permissions"></a>Oprávnění správce hesel

Může resetovat hesla pro jiné než správce a správce hesel.

| **Akce** | **Popis** |
| --- | --- |
| soubor microsoft.directory/users/password/update | Aktualizujte hesla pro všechny uživatele služby Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator-permissions"></a>Oprávnění správce služby Power BI

Zvládne spravovat všechny aspekty produktu Power BI.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>
| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Spravujte všechny aspekty Power BI. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |


### <a name="power-platform-administrator-permissions"></a>Oprávnění správce platformy Power Platform

Můžete vytvářet a spravovat všechny aspekty Microsoft Dynamics 365, PowerApps a Microsoft Flow. 

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>
| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.dynamics365/allEntities/allTasks | Spravujte všechny aspekty aplikace Dynamics 365. |
| Microsoft.flow/allEntities/allTasks | Spravujte všechny aspekty Microsoft Flow. |
| microsoft.powerApps/allEntities/allTasks | Spravujte všechny aspekty PowerApps. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="privileged-authentication-administrator-permissions"></a>Oprávnění správce privilegovaného ověřování

Povoleno zobrazit, nastavit a obnovit informace o metodě ověřování pro libovolného uživatele (správce nebo jiného správce).

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/users/invalidateAllRefreshTokens | Zneplatnit všechny tokeny aktualizace uživatelů ve službě Azure Active Directory. |
| Microsoft.directory/users/strongAuthentication/update | Aktualizujte silné vlastnosti ověřování, jako jsou informace o pověření mfa. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| soubor microsoft.directory/users/password/update | Aktualizujte hesla pro všechny uživatele v organizaci Office 365. Další podrobnosti najdete v online dokumentaci. |

### <a name="privileged-role-administrator-permissions"></a>Oprávnění správce privilegovaných rolí

Můžete spravovat přiřazení rolí ve službě Azure AD a všechny aspekty správy privilegovaných identit.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Vytvořte a odstraňte všechny prostředky a přečtěte si a aktualizujte standardní vlastnosti v microsoft.aad.privilegedIdentityManagement. |
| Microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | Čtení a konfigurace služby ServicePrincipals.appRoleAssignedTo ve službě Azure Active Directory |
| Microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Čtení a konfigurace servicePrincipals.oAuth2PermissionGrants vlastnost ve službě Azure Active Directory. |
| Microsoft.directory/administrativeUnits/allProperties/allTasks | Vytváření a správa administrativních jednotek (včetně členů) |
| Microsoft.directory/roleAssignments/allProperties/allTasks | Vytvořte a spravujte přiřazení rolí. |
| Microsoft.directory/roleDefinitions/allProperties/allTasks | Vytvořte a spravujte definice rolí. |

### <a name="reports-reader-permissions"></a>Oprávnění čtečky sestav

Umí číst zprávy o přihlášení a auditu.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/auditLogs/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs ve službě Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) ve službě SignInReports ve službě Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy využití Office 365. |

### <a name="search-administrator-permissions"></a>Oprávnění správce vyhledávání

Můžete vytvářet a spravovat všechny aspekty nastavení služby Microsoft Search.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.office365.messageCentrum/zprávy/čtení | Čtení zpráv v aplikaci microsoft.office365.messageCenter |
| microsoft.office365.search/allEntities/allProperties/allTasks | Vytvořte a odstraňte všechny prostředky a přečtěte si a aktualizujte všechny vlastnosti v souboru Microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |

### <a name="search-editor-permissions"></a>Oprávnění editoru hledání

Může vytvářet a spravovat redakční obsah, jako jsou záložky, Q a As, místa, půdorys.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.office365.messageCentrum/zprávy/čtení | Čtení zpráv v aplikaci microsoft.office365.messageCenter |
| microsoft.office365.search/content/allProperties/allTasks | Vytvořte a odstraňte obsah a přečtěte si a aktualizujte všechny vlastnosti v souboru Microsoft.office365.search. |

### <a name="security-administrator-permissions"></a>Oprávnění správce zabezpečení

Můžete číst informace o zabezpečení a sestavy a spravovat konfiguraci ve službě Azure AD a Office 365.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Soubor Microsoft.directory/applications/policies/update | Aktualizujte vlastnost applications.policies ve službě Azure Active Directory. |
| Microsoft.directory/auditLogs/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs ve službě Azure Active Directory. |
| Microsoft.directory/devices/bitLockerRecoveryKeys/read | Přečtěte si vlastnost devices.bitLockerRecoveryKeys ve službě Azure Active Directory. |
| Soubor Microsoft.directory/policies/basic/update | Aktualizujte základní vlastnosti zásad ve službě Azure Active Directory. |
| Microsoft.directory/policies/create | Vytvořte zásady ve službě Azure Active Directory. |
| Microsoft.directory/policies/delete | Odstraňte zásady ve službě Azure Active Directory. |
| Soubor Microsoft.directory/policies/owners/update | Aktualizujte vlastnost policies.owners ve službě Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Aktualizujte vlastnost policies.tenantDefault ve službě Azure Active Directory. |
| Microsoft.directory/servicePrincipals/policies/update | Aktualizujte vlastnost servicePrincipals.policies ve službě Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) ve službě SignInReports ve službě Azure Active Directory. |
| Microsoft.aad.identityProtection/allEntities/read | Přečtěte si všechny prostředky v microsoft.aad.identityProtection. |
| Microsoft.aad.identityProtection/allEntities/update | Aktualizujte všechny prostředky v aplikaci microsoft.aad.identityProtection. |
| Microsoft.aad.privilegedIdentityManagement/allEntities/read | Přečtěte si všechny prostředky v microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.protectionCentrum/allEntities/číst | Přečtěte si všechny aspekty Centra ochrany Office 365. |
| Microsoft.office365.protectionCentrum/všechny entity/aktualizace | Aktualizujte všechny prostředky v aplikaci Microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |

### <a name="security-operator-permissions"></a>Oprávnění operátora zabezpečení

Vytváří a spravuje události zabezpečení.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Přečtěte si a nakonfigurujte Microsoft Cloud App Security. |
| Microsoft.aad.identityProtection/allEntities/read | Přečtěte si všechny prostředky v microsoft.aad.identityProtection. |
| Microsoft.aad.privilegedIdentityManagement/allEntities/read | Přečtěte si všechny prostředky v microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Přečtěte si a nakonfigurujte Azure AD Advanced Threat Protection. |
| Microsoft.intune/allEntities/allTasks | Spravujte všechny aspekty Intune. |
| Microsoft.office365.securityComplianceCenter/allEntities/allTasks | Přečtěte si a nakonfigurujte Centrum dodržování předpisů & zabezpečení. |
| Microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Přečtěte si a nakonfigurujte pokročilou ochranu před internetovými hrozbami v programu Windows Defender. |

### <a name="security-reader-permissions"></a>Oprávnění čtečky zabezpečení

Můžete číst informace o zabezpečení a sestavy ve službě Azure AD a Office 365.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/auditLogs/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) na auditLogs ve službě Azure Active Directory. |
| Microsoft.directory/devices/bitLockerRecoveryKeys/read | Přečtěte si vlastnost devices.bitLockerRecoveryKeys ve službě Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/read | Přečtěte si vlastnost policies.conditionalAccess ve službě Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Přečtěte si všechny vlastnosti (včetně privilegovaných vlastností) ve službě SignInReports ve službě Azure Active Directory. |
| Microsoft.aad.identityProtection/allEntities/read | Přečtěte si všechny prostředky v microsoft.aad.identityProtection. |
| Microsoft.aad.privilegedIdentityManagement/allEntities/read | Přečtěte si všechny prostředky v microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.protectionCentrum/allEntities/číst | Přečtěte si všechny aspekty Centra ochrany Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |

### <a name="service-support-administrator-permissions"></a>Oprávnění správce podpory služeb

Může číst informace o stavu služby a spravovat lístky podpory.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

### <a name="sharepoint-service-administrator-permissions"></a>Oprávnění správce služby SharePoint Service

Může spravovat všechny aspekty služby SharePoint.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aktualizujte vlastnost groups.unified ve službě Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aktualizujte základní vlastnosti skupin Office 365. |
| microsoft.directory/groups/unified/create | Vytvořte skupiny Office 365. |
| soubor microsoft.directory/groups/unified/delete | Odstranění skupin Office 365 |
| Microsoft.directory/groups/unified/members/update | Aktualizujte členství ve skupinách Office 365. |
| Microsoft.directory/groups/unified/owners/update Microsoft.directory/groups/Unified/owners/update Microsoft.directory/groups/Unified/owners/update Microsoft. | Aktualizujte vlastnictví skupin Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Vytvořte a odstraňte všechny prostředky a přečtěte si a aktualizujte standardní vlastnosti v aplikaci Microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read    | Přečtěte si sestavy využití Office 365. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |

### <a name="teams-communications-administrator-permissions"></a>Oprávnění správce komunikace teams

Můžete spravovat funkce volání a schůzek v rámci služby Microsoft Teams.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy využití Office 365. |

### <a name="teams-communications-support-engineer-permissions"></a>Oprávnění teams communications support engineer

Dokáže řešit problémy s komunikací v Teams pomocí pokročilých nástrojů.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |

### <a name="teams-communications-support-specialist-permissions"></a>Oprávnění specialisty na odbornou pomoc teams communications support

Dokáže řešit problémy s komunikací v Teams pomocí základních nástrojů.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |

### <a name="teams-service-administrator-permissions"></a>Oprávnění správce služby Teams

Můžete spravovat službu Microsoft Teams.

> [!NOTE]
> Tato role má další oprávnění mimo službu Azure Active Directory. Další informace naleznete výše v popisu role.
>
>

| **Akce** | **Popis** |
| --- | --- |
| Microsoft.directory/groups/hiddenMembers/read | Ve službě Azure Active Directory si přečtěte vlastnost groups.hiddenMembers. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Aktualizujte vlastnost groups.unified ve službě Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Aktualizujte základní vlastnosti skupin Office 365. |
| microsoft.directory/groups/unified/create | Vytvořte skupiny Office 365. |
| soubor microsoft.directory/groups/unified/delete | Odstranění skupin Office 365 |
| Microsoft.directory/groups/unified/members/update | Aktualizujte členství ve skupinách Office 365. |
| Microsoft.directory/groups/unified/owners/update Microsoft.directory/groups/Unified/owners/update Microsoft.directory/groups/Unified/owners/update Microsoft. | Aktualizujte vlastnictví skupin Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |
| microsoft.office365.usageReports/allEntities/read | Přečtěte si sestavy využití Office 365. |

### <a name="user-administrator-permissions"></a>Oprávnění správce uživatelů
Může spravovat všechny aspekty uživatelů a skupin, včetně resetování hesel pro omezené správce.

| **Akce** | **Popis** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Vytvořte appRoleAssignments ve službě Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | Odstraňte úkoly role aplikace ve službě Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | Aktualizujte aplikaceRoleAssignments ve službě Azure Active Directory. |
| Microsoft.directory/contacts/basic/update | Aktualizujte základní vlastnosti kontaktů ve službě Azure Active Directory. |
| microsoft.directory/contacts/create | Vytvořte kontakty ve službě Azure Active Directory. |
| Microsoft.directory/contacts/delete | Odstraňte kontakty ve službě Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Aktualizujte vlastnost groups.appRoleAssignments ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/basic/update | Aktualizujte základní vlastnosti skupin ve službě Azure Active Directory. |
| Microsoft.directory/groups/create | Vytvořte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/createAsOwner | Vytvořte skupiny ve službě Azure Active Directory. Tvůrce je přidán jako první vlastník a vytvořený objekt se započítá v rámci kvóty 250 vytvořených objektů tvůrce. |
| Microsoft.directory/groups/delete | Odstraňte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/hiddenMembers/read | Ve službě Azure Active Directory si přečtěte vlastnost groups.hiddenMembers. |
| soubor microsoft.directory/groups/members/update | Aktualizujte vlastnost groups.members ve službě Azure Active Directory. |
| soubor microsoft.directory/groups/owners/update | Aktualizujte vlastnost groups.owners ve službě Azure Active Directory. |
| Microsoft.directory/groups/restore | Obnovte skupiny ve službě Azure Active Directory. |
| Microsoft.directory/groups/settings/update | Aktualizujte vlastnost groups.settings ve službě Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Aktualizujte vlastnost users.appRoleAssignments ve službě Azure Active Directory. |
| microsoft.directory/users/assignLicense | Správa licencí pro uživatele ve službě Azure Active Directory |
| Microsoft.directory/users/basic/update | Aktualizujte základní vlastnosti uživatelů ve službě Azure Active Directory. |
| Microsoft.directory/users/create | Vytvářejte uživatele ve službě Azure Active Directory. |
| microsoft.directory/users/delete | Odstraňte uživatele ve službě Azure Active Directory. |
| Microsoft.directory/users/invalidateAllRefreshTokens | Zneplatnit všechny tokeny aktualizace uživatelů ve službě Azure Active Directory. |
| microsoft.directory/users/manager/update | Aktualizujte vlastnost users.manager ve službě Azure Active Directory. |
| soubor microsoft.directory/users/password/update | Aktualizujte hesla pro všechny uživatele služby Azure Active Directory. Další podrobnosti najdete v online dokumentaci. |
| Microsoft.directory/users/restore | Obnovení odstraněných uživatelů ve službě Azure Active Directory |
| Microsoft.directory/users/userPrincipalName/update | Aktualizujte vlastnost users.userPrincipalName ve službě Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Azure. |
| Microsoft.office365.webPortal/allEntities/basic/read | Přečtěte si základní vlastnosti všech prostředků na portálu microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Přečtěte si a nakonfigurujte stav služby Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Vytvářejte a spravujte lístky podpory Office 365. |

## <a name="role-template-ids"></a>ID šablony rolí

ID šablony rolí používají hlavně rozhraní Microsoft Graph API nebo uživatelé prostředí PowerShell.

Grafický_název | Zobrazovaný název portálu Azure | adresářRoleTemplateId
----------------- | ------------------------- | -------------------------
Správce aplikace | Správce aplikace | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Vývojář aplikace | Vývojář aplikací | CF1C38E5-3621-4004-A7CB-879624DCED7C
Správce ověřování | Správce ověřování | c4e39bd9-1100-46d3-8c65-fb160da0071f
Správce Azure DevOps | Správce Azure DevOps | e3973bdf-4987-49ae-837a-ba8e231c7286
Správce ochrany informací Azure | Správce azure information protection | 7495fdc4-34c4-4d15-a289-98788ce399fd
Správce toku uživatele B2C | Správce toku uživatele B2C | 6e591065-9bad-43ed-90f3-e9424366d2f0
Správce atributů toku uživatele B2C | Správce atributů toku uživatele B2C | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
Správce sady klíčů B2C IEF | Správce sady klíčů B2C IEF | aaf43236-0c0d-4d5f-883a-6955382ac081
Správce zásad B2C IEF | Správce zásad B2C IEF | 3edaf663-341e-4475-9f94-5c398ef6c070
Správce fakturace | Správce fakturace | b0f54661-2d74-4c50-afa3-1ec803f12efe
Správce cloudové aplikace | Správce cloudových aplikací | 158c047a-c907-4556-b7ef-446551a6b5f7
Správce cloudových zařízení | Správce cloudových zařízení | 7698a772-787b-4ac8-901f-60d6b08affd2
Správce společnosti | Globální správce | 62e90394-69f5-4237-9190-012177145e10
Správce dodržování předpisů | Správce dodržování předpisů | 17315797-102d-40b4-93e0-432062caca18
Správce dat pro dodržování předpisů | Správce dat dodržování předpisů | e6d1a23a-da11-4be4-9570-befc86d067a7
Správce podmíněného přístupu | Správce podmíněného přístupu | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Správce služby CRM | Správce dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Schvalovatel přístupu k poštovnímu panelu zákazníka | Schvalovatel přístupu k bezpečnostnímu schránku zákazníka | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Správce analýzy plochy | Správce analýzy plochy | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Správci služeb | Správci zařízení | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Připojení k zařízení | Spojení zařízení | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Správci zařízení | Správci zařízení | 2b499bcd-da44-4968-8aec-78e1674fa64d
Uživatelé zařízení | Uživatelé zařízení | d405c6df-0af8-4e3b-95e4-4d06e542189e
Čtečky adresářů | Čtečky adresářů | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Účty synchronizace adresářů | Účty synchronizace adresářů | d29b2b05-8046-44ba-8758-1e26182fcf32
Adresář spisovatelů | Adresář spisovatelů | 9360feb5-f418-4baa-8175-e2a00bac4301
Správce služby Exchange Service | Správce Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Správce externího zprostředkovatele identity | Správce externího zprostředkovatele identity | be2f45a1-457d-42af-a067-6ec1fa63bc45
Globální čtečka | Globální čtečka | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Správce skupin | Správce skupin | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Pozvaní hosté | Hostující pozvač | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Správce technické podpory | Správce technické podpory | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Správce služby Intune | Správce Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala Správce | Kaizala správce | 74ef975b-6605-40af-a5d2-b9539d836353
Správce licencí | Správce licence | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Správce služby Lync | Správce Skypu pro firmy | 75941009-915a-4869-abe7-691bff18279e
Čtečka ochrany osobních údajů Centra zpráv | Čtečka ochrany osobních údajů centra zpráv | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Čtečka Centra zpráv | Čtečka centra zpráv | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Správce aplikací Office | Správce aplikací Office | 2b745bdf-0803-4d80-aa65-822c4493daac
Podpora partnerské úrovně1 | Podpora partnerské úrovně1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Podpora partnerské úrovně2 | Podpora partnerské úrovně2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Správce hesel | Správce hesel | 966707d0-3269-4727-9be2-8c3a10f19b9d
Správce služby Power BI | Správce Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Správce napájecí platformy | Správce napájecí platformy | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Správce privilegovaného ověřování | Správce privilegovaného ověřování | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Správce privilegovaných rolí | Správce privilegovaných rolí | e8611ab8-c189-46e8-94e1-60213ab1f814
Čtečka sestav | Čtečka sestav | 4a5d8f65-41da-4de4-8968-e035b65339cf
Správce hledání | Správce hledání | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Editor vyhledávání | Editor vyhledávání | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Správce zabezpečení | Správce zabezpečení | 194ae4cb-b126-40b2-bd5b-6091b380977d
Operátor zabezpečení | Bezpečnostní operátor | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Čtečka zabezpečení | Čtenář zabezpečení | 5d6b6bb7-de71-4623-b4af-96380a352509
Správce podpory služby | Správce služeb | f023fd81-a637-4b56-95fd-791ac0226033
Správce služby SharePoint Service | Správce SharePointu | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Správce komunikace teams | Správce komunikace teams | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Technik podpory komunikace teams | Technik podpory komunikace teams | f70938a0-fc10-4177-9e90-2178f8765737
Specialista na podporu komunikace týmů | Specialista na podporu komunikace týmů | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 fcf91098-03e3-41a9-b5ba-6f0ec8188a12 fcf91098-03e3-41a9-b5ba-6f0ec8188a12 fcf
Správce služby Teams | Správce služby Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Uživatel | Uživatel | a0b1b346-4d3e-4e8b-98f8-753987be4970
Správce uživatelského účtu | Správce uživatele | fe930be7-5e62-47db-91af-98c3a49a38b1
Připojení k zařízení na pracovišti | Spojení zařízení na pracovišti | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Zastaralé role

Následující role by neměly být použity. Byly zastaralé a budou odebrány z Azure AD v budoucnu.

* Správce licencí AdHoc
* Připojení k zařízení
* Správci zařízení
* Uživatelé zařízení
* E-mail ověřený uživatel tvůrce
* Správce poštovní schránky
* Připojení k zařízení na pracovišti

## <a name="roles-not-shown-in-the-portal"></a>Role, které nejsou zobrazeny na portálu

Ne každá role vrácená powershellem nebo rozhraním MS Graph API je viditelná na webu Azure Portal. Následující tabulka uspořádá tyto rozdíly.

Název rozhraní API | Název portálu Azure | Poznámky
-------- | ------------------- | -------------
Správce společnosti | Globální správce | [Název změněn pro lepší přehlednost](directory-assign-admin-roles.md#role-template-ids)
Správce služby CRM | Správce dynamics 365 | [Odráží aktuální značku produktu](directory-assign-admin-roles.md#role-template-ids)
Připojení k zařízení | Zastaralé | [Dokumentace zastaralá role](directory-assign-admin-roles.md#deprecated-roles)
Správci zařízení | Zastaralé | [Dokumentace zastaralá role](directory-assign-admin-roles.md#deprecated-roles)
Uživatelé zařízení | Zastaralé | [Dokumentace zastaralá role](directory-assign-admin-roles.md#deprecated-roles)
Účty synchronizace adresářů | Nezobrazuje se, protože by neměl být používán | [Dokumentace k účtům synchronizace adresářů](directory-assign-admin-roles.md#directory-synchronization-accounts)
Adresář spisovatelů | Nezobrazuje se, protože by neměl být používán | [Dokumentace k autorům adresářů](directory-assign-admin-roles.md#directory-writers)
Uživatel typu host | Není zobrazeno, protože jej nelze použít  | Není k dispozici
Správce služby Lync | Správce Skypu pro firmy | [Odráží aktuální značku produktu](directory-assign-admin-roles.md#role-template-ids)
Podpora partnerské úrovně 1 | Nezobrazuje se, protože by neměl být používán | [Dokumentace podpory Tier1 partnera](directory-assign-admin-roles.md#partner-tier1-support)
Podpora partnerské úrovně 2 | Nezobrazuje se, protože by neměl být používán | [Dokumentace podpory Tier2 partnera](directory-assign-admin-roles.md#partner-tier2-support)
Správce tiskárny | Probíhající práce | Probíhající práce
Technik tiskárny | Probíhající práce | Probíhající práce
Omezený uživatel typu Host | Není zobrazeno, protože jej nelze použít | Není k dispozici
Uživatel | Není zobrazeno, protože jej nelze použít | Není k dispozici
Připojení k zařízení na pracovišti | Zastaralé | [Dokumentace zastaralá role](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak přiřadit uživatele jako správce předplatného Azure, najdete v tématu [Správa přístupu pomocí rolí Azure (Azure RBAC).](../../role-based-access-control/role-assignments-portal.md)
* Další informace o tom, jak se v Microsoft Azure řídí přístup k prostředkům, najdete [v tématu Principy různých rolí.](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Další informace o vztahu Azure Active Directory k předplatnému Azure najdete v tématu [Jak je předplatné Azure propojeno se službou Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
