---
title: Předdefinované role Azure AD – Azure Active Directory
description: Popisuje Azure Active Directory předdefinované role a oprávnění.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 04/06/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e356363d91c3aa429fd958c1dd3bc553e7a4d29
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030232"
---
# <a name="azure-ad-built-in-roles"></a>Předdefinované role Azure AD

Pokud v Azure Active Directory (Azure AD) potřebuje jiný správce nebo správce, než je potřeba spravovat prostředky služby Azure AD, přiřadíte jim roli Azure AD, která poskytuje potřebná oprávnění. Můžete například přiřadit role, abyste mohli přidávat nebo měnit uživatele, resetovat hesla uživatelů, spravovat uživatelské licence nebo spravovat názvy domén.

V tomto článku jsou uvedené předdefinované role Azure AD, které můžete přiřadit, aby bylo možné spravovat prostředky služby Azure AD. Informace o tom, jak přiřadit role, najdete v tématu [přiřazení rolí Azure AD uživatelům](manage-roles-portal.md).

## <a name="all-roles"></a>Všechny role

> [!div class="mx-tableFixed"]
> | Role | Popis | ID šablony |
> | --- | --- | --- |
> | [Správce aplikace](#application-administrator) | Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Vývojář aplikace](#application-developer) | Může vytvořit registrace aplikací nezávisle na nastavení uživatelé můžou registrovat aplikace. | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Autor datové části útoku](#attack-payload-author) | Může vytvořit datovou část útoku, kterou může správce zahájit později. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Správce simulace útoků](#attack-simulation-administrator) | Může vytvářet a spravovat všechny aspekty simulace útoků. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Správce ověřování](#authentication-administrator) | Má přístup k zobrazení, nastavení a resetování informací o metodě ověřování pro libovolného uživatele bez oprávnění správce. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Správce zásad ověřování](#authentication-policy-administrator) | Může vytvářet a spravovat všechny aspekty metod ověřování a zásad ochrany heslem. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Místní správce zařízení připojený k Azure AD](#azure-ad-joined-device-local-administrator) | Uživatelé přiřazení k této roli budou přidáni do místní skupiny Administrators na zařízeních připojených k Azure AD. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Správce Azure DevOps](#azure-devops-administrator) | Může spravovat zásady a nastavení organizace Azure DevOps. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Správce Azure Information Protection](#azure-information-protection-administrator) | Může spravovat všechny aspekty Azure Information Protection produktu. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [B2C IEF, správce sady klíčů](#b2c-ief-keyset-administrator) | Může spravovat tajné klíče pro federaci a šifrování v rozhraní IEF (identity Experience Framework). | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [Správce zásad IEF B2C](#b2c-ief-policy-administrator) | Může vytvářet a spravovat zásady architektury pro vztahy důvěryhodnosti v rozhraní IEF (identity Experience Framework). | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Správce fakturace](#billing-administrator) | Může provádět běžné úkoly související s fakturací, jako je aktualizace platebních údajů. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Správce cloudové aplikace](#cloud-application-administrator) | Může vytvářet a spravovat všechny aspekty registrací aplikací a podnikových aplikací kromě proxy aplikací. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Správce cloudového zařízení](#cloud-device-administrator) | Omezený přístup ke správě zařízení ve službě Azure AD. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Správce dodržování předpisů](#compliance-administrator) | Může číst a spravovat konfiguraci a sestavy dodržování předpisů v Azure AD a Microsoft 365. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Správce dat dodržování předpisů](#compliance-data-administrator) | Vytvoří a spravuje obsah dodržování předpisů. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Správce podmíněného přístupu](#conditional-access-administrator) | Může spravovat funkce podmíněného přístupu. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Schvalovatel přístupu k bezpečnostnímu modulu zákazníka](#customer-lockbox-access-approver) | Může schvalovat žádosti o podporu Microsoftu pro přístup k datům organizace zákazníka. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Správce Desktop Analytics](#desktop-analytics-administrator) | Umožňuje přístup k nástrojům a službám správy plochy a jejich správa. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Čtečky adresářů](#directory-readers) | Může číst základní informace o adresáři. Běžně se používá pro udělení přístupu ke čtení adresáře aplikacím a hostům. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Účty synchronizace adresářů](#directory-synchronization-accounts) | Používáno pouze službou Azure AD Connect. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Zapisovače adresářů](#directory-writers) | Může číst a zapisovat základní informace o adresáři. Pro udělení přístupu k aplikacím, které nejsou určené pro uživatele. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Správce názvů domén](#domain-name-administrator) | Může spravovat názvy domén v cloudu i v místním prostředí. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Správce Dynamics 365](#dynamics-365-administrator) | Může spravovat všechny aspekty produktu Dynamics 365. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Správce Exchange](#exchange-administrator) | Může spravovat všechny aspekty produktu Exchange. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Správce příjemce Exchange](#exchange-recipient-administrator) | Může vytvořit nebo aktualizovat příjemce Exchange Online v rámci organizace Exchange Online. | 31392ffb-586c-42d1-9346-e59415a2cc4e |
> | [Správce toku externího ID uživatele](#external-id-user-flow-administrator) | Může vytvářet a spravovat všechny aspekty toků uživatelů. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Správce atributů toku uživatele externího ID](#external-id-user-flow-attribute-administrator) | Může vytvořit a spravovat schéma atributů dostupné pro všechny toky uživatelů. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Správce externích zprostředkovatelů identity](#external-identity-provider-administrator) | Může nakonfigurovat zprostředkovatele identity pro použití v přímé federaci. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Globální správce](#global-administrator) | Může spravovat všechny aspekty Azure AD a služeb Microsoftu, které používají identity Azure AD. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Globální čtenář](#global-reader) | Může číst vše, co globální správce může, ale ne vše aktualizovat. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Správce skupin](#groups-administrator) | Členové této role mohou vytvářet a spravovat skupiny, vytvářet a spravovat nastavení skupin, jako jsou zásady vytváření názvů a vypršení platnosti, a zobrazit aktivity skupin a sestavy auditování. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Pozvánka hosta](#guest-inviter) | Může pozvat uživatele typu Host bez ohledu na nastavení členové můžou pozvat hosty. | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Správce helpdesku](#helpdesk-administrator) | Může resetovat hesla pro správce bez oprávnění správce a helpdesku. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Správce hybridní identity](#hybrid-identity-administrator) | Může spravovat AD do zřizování cloudu Azure AD, Azure AD Connect a nastavení federace. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Správce Insights](#insights-administrator) | Má v aplikaci Microsoft 365 Insights přístup pro správu. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Vedoucí firmy pro Insights](#insights-business-leader) | Může zobrazit a sdílet řídicí panely a přehledy prostřednictvím aplikace M365 Insights. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Správce Intune](#intune-administrator) | Může spravovat všechny aspekty produktu Intune. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Správce Kaizala](#kaizala-administrator) | Může spravovat nastavení pro Microsoft Kaizala. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Správce znalostní báze](#knowledge-administrator) | Může konfigurovat znalosti, učení a další inteligentní funkce. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Správce licencí](#license-administrator) | Může spravovat licence na produkty pro uživatele a skupiny. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Čtenář ochrany osobních údajů centra zpráv](#message-center-privacy-reader) | Může číst zprávy zabezpečení a aktualizace pouze v centru zpráv systému Office 365. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Čtenář centra zpráv](#message-center-reader) | Může číst zprávy a aktualizace pro svou organizaci jenom v centru zpráv Office 365. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Moderní uživatel pro obchodování](#modern-commerce-user) | Může spravovat komerční nákupy pro firmu, oddělení nebo tým. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Správce sítě](#network-administrator) | Může spravovat síťová umístění a prohlížet informace o návrhu podnikové sítě, které Microsoft 365 software jako aplikace služby. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Správce aplikací Office](#office-apps-administrator) | Může spravovat aplikace Office Cloud Services, včetně správy zásad a nastavení, a spravovat možnost výběru, zrušení výběru a publikování obsahu funkcí co je nového pro zařízení koncových uživatelů. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [Podpora partnerů Tier1](#partner-tier1-support) | Nepoužívejte – Neurčeno pro obecné použití. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Podpora partnerů 2](#partner-tier2-support) | Nepoužívejte – Neurčeno pro obecné použití. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Správce hesel](#password-administrator) | Může resetovat hesla správců, kteří nejsou správci a hesla. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Správce Power BI](#power-bi-administrator) | Může spravovat všechny aspekty Power BI produktu. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Správce Power Platform](#power-platform-administrator) | Může vytvářet a spravovat všechny aspekty Microsoft Dynamics 365, PowerApps a Microsoft Flow. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Správce tiskárny](#printer-administrator) | Může spravovat všechny aspekty tiskáren a konektorů tiskáren. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Technik tiskárny](#printer-technician) | Může registrovat a rušit registraci tiskáren a aktualizovat stav tiskárny. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Správce privilegovaného ověřování](#privileged-authentication-administrator) | Má přístup k zobrazení, nastavení a resetování informací o metodě ověřování pro libovolného uživatele (správce nebo správce bez oprávnění správce). | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Správce privilegovaných rolí](#privileged-role-administrator) | Může spravovat přiřazení rolí v Azure AD a všechny aspekty Privileged Identity Management. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Čtečka sestav](#reports-reader) | Může číst sestavy pro přihlášení a audit. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Správce hledání](#search-administrator) | Může vytvářet a spravovat všechny aspekty nastavení služby Microsoft Search. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Editor hledání](#search-editor) | Může vytvářet a spravovat redakční obsah, jako jsou záložky, Q a as, Locations, FloorPlan. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Správce zabezpečení](#security-administrator) | Může číst informace o zabezpečení a sestavy a spravovat konfiguraci v Azure AD a Office 365. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Operátor zabezpečení](#security-operator) | Vytvoří a spravuje události zabezpečení. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Čtenář zabezpečení](#security-reader) | Může číst informace o zabezpečení a sestavy v Azure AD a Office 365. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Správce služby Service support](#service-support-administrator) | Může číst informace o stavu služby a spravovat lístky podpory. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [Správce služby SharePoint](#sharepoint-administrator) | Může spravovat všechny aspekty služby SharePoint. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Správce Skypu pro firmy](#skype-for-business-administrator) | Může spravovat všechny aspekty produktu Skype pro firmy. | 75941009-915a-4869-abe7-691bff18279e |
> | [Správce týmů](#teams-administrator) | Může spravovat službu Microsoft Teams. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Správce komunikace týmů](#teams-communications-administrator) | Může spravovat funkce volání a schůzek ve službě Microsoft Teams. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Týmy Communications support inženýr](#teams-communications-support-engineer) | Může řešit problémy s komunikací v rámci týmů pomocí pokročilých nástrojů. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Týmy Communications support specialisty](#teams-communications-support-specialist) | Může řešit problémy s komunikací v rámci týmů pomocí základních nástrojů. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Správci zařízení Teams](#teams-devices-administrator) | Může provádět úlohy související se správou na certifikovaných zařízeních týmů. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Čtečka sestav Souhrn využití](#usage-summary-reports-reader) | V Microsoft 365 analýza využití a skóre produktivity se můžou zobrazit jenom agregace na úrovni tenanta. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Správce uživatele](#user-administrator) | Může spravovat všechny aspekty uživatelů a skupin, včetně resetování hesel pro omezené správce. | fe930be7-5e62-47db-91af-98c3a49a38b1 |

## <a name="application-administrator"></a>Správce aplikace

Uživatelé v této roli můžou vytvářet a spravovat všechny aspekty podnikových aplikací, registrací aplikací a nastavení proxy aplikací. Všimněte si, že uživatelé přiřazení k této roli se při vytváření nových registrací aplikací nebo podnikových aplikací nepřidali jako vlastníci.

Tato role také uděluje možnost udělit souhlas pro delegovaná oprávnění a oprávnění aplikací s výjimkou oprávnění aplikace pro Microsoft Graph i Azure AD Graph.

> [!IMPORTANT]
> Tato výjimka znamená, že stále můžete vyjádřit souhlas s oprávněními aplikace pro _ostatní_ aplikace (například aplikace od jiných společností než Microsoft nebo aplikace, které jste zaregistrovali). Tato oprávnění si můžete přesto _vyžádat_ jako součást registrace aplikace, ale _udělení_ (tj. přijetí) vyžaduje oprávnění správce, jako je například globální správce.
>
>Tato role uděluje možnost spravovat přihlašovací údaje aplikací. Uživatelé přiřazení k této roli můžou do aplikace přidat přihlašovací údaje a pomocí těchto přihlašovacích údajů zosobnit identitu aplikace. Pokud má identita aplikace udělen přístup k prostředku, jako je třeba možnost vytvořit nebo aktualizovat uživatele nebo jiné objekty, může uživatel přiřazený k této roli provádět tyto akce při zosobnění aplikace. Tato schopnost zosobnit identitu aplikace může být zvýšením oprávnění, přes co může uživatel dělat prostřednictvím přiřazení rolí. Je důležité pochopit, že přiřazení uživatele k roli správce aplikace jim dává možnost zosobnit identitu aplikace.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/aplikace/vytvořit | Vytvořit všechny typy aplikací |
> | Microsoft. Directory/aplikace/odstranit | Odstranit všechny typy aplikací |
> | Microsoft. Directory/Applications/applicationProxy/Read | Číst všechny vlastnosti proxy aplikace |
> | Microsoft. Directory/Applications/applicationProxy/Update | Aktualizovat vlastnosti proxy všech aplikací |
> | Microsoft. Directory/Applications/applicationProxyAuthentication/Update | Aktualizovat ověřování u všech typů aplikací |
> | Microsoft. Directory/Applications/applicationProxySslCertificate/Update | Aktualizovat nastavení certifikátu SSL pro proxy aplikací |
> | Microsoft. Directory/Applications/applicationProxyUrlSettings/Update | Aktualizovat nastavení adresy URL pro proxy aplikací |
> | Microsoft. Directory/Applications/appRoles/Update | Aktualizuje vlastnost appRoles u všech typů aplikací. |
> | Microsoft. Directory/Applications/publikum/Update | Aktualizace vlastnosti cílové skupiny pro aplikace |
> | Microsoft. Directory/Applications/Authentication/Update | Aktualizovat ověřování u všech typů aplikací |
> | Microsoft. Directory/Applications/Basic/Update | Aktualizace základních vlastností pro aplikace |
> | Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizovat přihlašovací údaje aplikace |
> | Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizovat vlastníky aplikací |
> | Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizovat vystavená oprávnění a požadovaná oprávnění pro všechny typy aplikací |
> | Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizovat zásady aplikací |
> | Microsoft. Directory/Applications/ověření/aktualizace | Aktualizovat vlastnost applicationsverification |
> | Microsoft. Directory/aplikace/synchronizace/standardní/číst | Čtení nastavení zřizování přidružených k objektu aplikace |
> | Microsoft. Directory/applicationTemplates/instance | Vytvoření instance aplikací galerie z šablon aplikací |
> | Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti v protokolech auditu, včetně privilegovaných vlastností. |
> | Microsoft. Directory/konektory/vytvořit | Vytváření konektorů proxy aplikací |
> | Microsoft. Directory/Connectors/allProperties/Read | Načte všechny vlastnosti konektorů proxy aplikací. |
> | Microsoft. Directory/connectorGroups/Create | Vytvořit skupiny konektorů proxy aplikací |
> | Microsoft. Directory/connectorGroups/DELETE | Odstranit skupiny konektorů proxy aplikací |
> | Microsoft. Directory/connectorGroups/allProperties/Read | Načte všechny vlastnosti skupin konektorů proxy aplikací. |
> | Microsoft. Directory/connectorGroups/allProperties/Update | Aktualizovat všechny vlastnosti skupin konektorů proxy aplikací |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Umožňuje vytvářet a odstraňovat oprávnění OAuth 2,0 a číst a aktualizovat všechny vlastnosti. |
> | Microsoft. Directory/applicationPolicies/Create | Vytvoření zásad použití |
> | Microsoft. Directory/applicationPolicies/DELETE | Odstranit zásady použití |
> | Microsoft. Directory/applicationPolicies/Standard/Read | Čtení standardních vlastností zásad použití |
> | Microsoft. Directory/applicationPolicies/Owners/Read | Čtení vlastníků u zásad použití |
> | Microsoft. Directory/applicationPolicies/policyAppliedTo/Read | Číst zásady použití pro seznam objektů |
> | Microsoft. Directory/applicationPolicies/Basic/Update | Aktualizace standardních vlastností zásad použití |
> | Microsoft. Directory/applicationPolicies/Owners/Update | Aktualizace vlastnosti Owner zásad použití |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování. |
> | Microsoft. Directory/servicePrincipals/Create | Vytvoření instančních objektů |
> | Microsoft. Directory/servicePrincipals/DELETE | Odstranění objektů služby |
> | Microsoft. Directory/servicePrincipals/Disable | Zakázat instanční objekty |
> | Microsoft. Directory/servicePrincipals/Enable | Povolení instančních objektů |
> | Microsoft. Directory/servicePrincipals/getPasswordSingleSignOnCredentials | Správa přihlašovacích údajů jednotného přihlašování k heslům u instančních objektů |
> | Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage | Správa tajných klíčů a přihlašovacích údajů pro zřizování aplikací |
> | Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Spusťte, restartujte a pozastavte úlohy synchronizace – zřizování aplikací. |
> | Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Vytváření a Správa synchronizace – úloh a schémat pro zřizování aplikací |
> | Microsoft. Directory/servicePrincipals/managePasswordSingleSignOnCredentials | Číst přihlašovací údaje jednotného přihlašování hesla u instančních objektů |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Application-admin | Udělení souhlasu pro oprávnění aplikace a delegovaná oprávnění jménem libovolného uživatele nebo všech uživatelů, s výjimkou oprávnění aplikace pro Microsoft Graph a Azure AD Graph |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizovat přiřazení rolí instančního objektu |
> | Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizovat vlastnosti cílové skupiny u instančních objektů |
> | Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizovat vlastnosti ověřování u instančních objektů |
> | Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizace základních vlastností u instančních objektů |
> | Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizovat přihlašovací údaje objektů služby |
> | Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizujte vlastníky instančních objektů. |
> | Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizovat oprávnění instančních objektů |
> | Microsoft. Directory/servicePrincipals/policies/Update | Aktualizovat zásady instančních objektů |
> | Microsoft. Directory/servicePrincipals/tag/Update | Aktualizace vlastnosti značky pro instanční objekty |
> | Microsoft. Directory/servicePrincipals/Synchronization/Standard/Read | Čtení nastavení zřizování přidružených k vašemu instančnímu objektu |
> | Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti v sestavách přihlášení, včetně privilegovaných vlastností. |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="application-developer"></a>Vývojář aplikace

Uživatelé v této roli můžou vytvářet registrace aplikací, když je nastavení "uživatelé můžou registrovat aplikace" nastavené na ne. Tato role také uděluje oprávnění k souhlasu jednoho vlastního uživatele, pokud "uživatelé můžou udělit souhlas s aplikacemi, které přistupují k firemním datům" jejich jménem "nastavení je nastaveno na ne. Uživatelé přiřazení k této roli jsou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Applications/createAsOwner | Vytvoří všechny typy aplikací a autor se přidá jako první vlastník. |
> | Microsoft. Directory/appRoleAssignments/createAsOwner | Vytvoření přiřazení aplikační role s autorem jako prvním vlastníkem |
> | Microsoft. Directory/oAuth2PermissionGrants/createAsOwner | Vytvoření udělení oprávnění OAuth 2,0 s autorem jako prvním vlastníkem |
> | Microsoft. Directory/servicePrincipals/createAsOwner | Vytvoření instančních objektů s autorem jako prvním vlastníkem |

## <a name="attack-payload-author"></a>Autor datové části útoku

Uživatelé v této roli můžou vytvářet datovou část útoku, ale nemůžou je ve skutečnosti spouštět nebo naplánovat. Datová část útoku je pak k dispozici všem správcům v tenantovi, kteří je můžou použít k vytvoření simulace.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. protectionCenter/attackSimulator/datové části/allProperties/allTasks | Vytváření a Správa datových částí útoku v simulátoru útoků |
> | Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/Read | Přečtěte si sestavy pro simulace útoků útoku a související školení. |

## <a name="attack-simulation-administrator"></a>Správce simulace útoků

Uživatelé v této roli můžou vytvářet a spravovat všechny aspekty vytvoření simulace útoku, spustit nebo naplánovat simulaci a zkontrolovat výsledky simulace. Členové této role mají tento přístup pro všechny simulace v tenantovi.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. protectionCenter/attackSimulator/datové části/allProperties/allTasks | Vytváření a Správa datových částí útoku v simulátoru útoků |
> | Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/Read | Přečtěte si sestavy pro simulace útoků útoku a související školení. |
> | Microsoft. Office 365. protectionCenter/attackSimulator/simulace/allProperties/allTasks | Vytváření a Správa šablon simulace útoků v simulátoru útoku |

## <a name="authentication-administrator"></a>Správce ověřování

Uživatelé s touto rolí můžou nastavit nebo resetovat jakoukoli metodu ověřování (včetně hesel) pro uživatele bez oprávnění správce a některé role. Správci ověřování můžou vyžadovat, aby uživatelé, kteří jsou bez oprávnění správce nebo se k některým rolím přiřadili, zaregistrovali v rámci stávajících přihlašovacích údajů, které nepoužívají heslo (například MFA nebo FIDO), a taky můžou **zapomenout** MFA na zařízení, která při příštím přihlášení VYZVE k MFA. Seznam rolí, které může správce ověřování číst nebo aktualizovat metody ověřování, najdete v tématu oprávnění k [resetování hesla](#password-reset-permissions).

Role [správce privilegovaného ověřování](#privileged-authentication-administrator) má oprávnění k vynucení opakované registrace a ověřování Multi-Factor Authentication pro všechny uživatele.

Role [správce zásad ověřování](#authentication-policy-administrator) má oprávnění k nastavení zásad ověřování klienta, které určuje, které metody může každý uživatel registrovat a používat.

| Role | Umožňuje spravovat metody ověřování uživatele. | Správa MFA pro jednotlivé uživatele | Správa nastavení vícefaktorového ověřování | Správa zásad metod ověřování | Spravovat zásady ochrany heslem |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Správce ověřování | Ano pro některé uživatele (viz výše) | Ano pro některé uživatele (viz výše) | No | No | No |
| Správce privilegovaného ověřování| Ano pro všechny uživatele | Ano pro všechny uživatele | No | No | No |
| Správce zásad ověřování | No |No | Yes | Yes | Yes |

> [!IMPORTANT]
> Uživatelé s touto rolí můžou měnit přihlašovací údaje pro uživatele, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i mimo Azure Active Directory. Změna přihlašovacích údajů uživatele může znamenat možnost předpokládat identitu a oprávnění tohoto uživatele. Například:
>
>* Registrace aplikace a vlastníci podnikových aplikací, kteří můžou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace můžou mít privilegovaná oprávnění ve službě Azure AD a jinde nejsou udělená správcům ověřování. Prostřednictvím této cesty může správce ověřování předpokládat identitu vlastníka aplikace a následně převzít identitu privilegované aplikace tím, že aktualizuje přihlašovací údaje pro aplikaci.
>* Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
>* Skupina zabezpečení a Microsoft 365 vlastníci skupiny, kteří mohou spravovat členství ve skupině. Tyto skupiny můžou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure AD a jinde.
>* Správci v jiných službách mimo Azure AD, jako je Exchange Online, Centrum zabezpečení a dodržování předpisů pro Office a systémy lidských zdrojů.
>* Nesprávci jako vedoucí pracovníci, právní poradce a zaměstnanci lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

> [!IMPORTANT]
> Tato role nemůže spravovat nastavení MFA na starším portálu pro správu MFA nebo v tokenech OATH pro hardware. Stejné funkce můžete provést pomocí modulu [set-MsolUser](/powershell/module/msonline/set-msoluser) RUTINY Azure AD PowerShell.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Vynutit odhlášení pomocí neověřování tokenů aktualizace uživatelů |
> | Microsoft. Directory/Users/strongAuthentication/Update | Aktualizace vlastnosti silného ověřování pro uživatele |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="authentication-policy-administrator"></a>Správce zásad ověřování

Uživatelé s touto rolí můžou konfigurovat zásady metod ověřování, nastavení VÍCEFAKTOROVÉHO ověřování na úrovni tenanta a zásady ochrany heslem. Tato role uděluje oprávnění ke správě nastavení ochrany heslem: Konfigurace inteligentního uzamknutí a aktualizace vlastního seznamu zakázaných hesel.

Role správce [ověřování](#authentication-administrator) a [správce privilegovaného ověřování](#privileged-authentication-administrator) mají oprávnění ke správě registrovaných metod ověřování pro uživatele a můžou vynutit opakovanou registraci a službu Multi-Factor Authentication pro všechny uživatele.

| Role | Umožňuje spravovat metody ověřování uživatele. | Správa MFA pro jednotlivé uživatele | Správa nastavení vícefaktorového ověřování | Správa zásad metod ověřování | Spravovat zásady ochrany heslem |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Správce ověřování | Ano pro některé uživatele (viz výše) | Ano pro některé uživatele (viz výše) | No | No | No |
| Správce privilegovaného ověřování| Ano pro všechny uživatele | Ano pro všechny uživatele | No | No | No |
| Správce zásad ověřování | No | No | Yes | Yes | Yes |

> [!IMPORTANT]
> Tato role nemůže spravovat nastavení MFA na starším portálu pro správu MFA nebo v tokenech OATH pro hardware.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Organization/strongAuthentication/Update | Aktualizace vlastností silného ověřování organizace |
> | Microsoft. Directory/userCredentialPolicies/Create | Vytvoření zásad přihlašovacích údajů pro uživatele |
> | Microsoft. Directory/userCredentialPolicies/DELETE | Odstranění zásad přihlašovacích údajů pro uživatele |
> | Microsoft. Directory/userCredentialPolicies/Standard/Read | Čtení standardních vlastností zásad přihlašovacích údajů pro uživatele |
> | Microsoft. Directory/userCredentialPolicies/Owners/Read | Čtení vlastníků zásad přihlašovacích údajů pro uživatele |
> | Microsoft. Directory/userCredentialPolicies/policyAppliedTo/Read | Přečíst navigační odkaz zásady. appliesTo |
> | Microsoft. Directory/userCredentialPolicies/Basic/Update | Aktualizace základních zásad pro uživatele |
> | Microsoft. Directory/userCredentialPolicies/Owners/Update | Aktualizace vlastníků zásad přihlašovacích údajů pro uživatele |
> | Microsoft. Directory/userCredentialPolicies/tenantDefault/Update | Aktualizovat vlastnost Policy. isOrganizationDefault |

## <a name="azure-ad-joined-device-local-administrator"></a>Místní správce zařízení připojený k Azure AD

Tato role je k dispozici pro přiřazení pouze jako další místní správce v [nastavení zařízení](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Uživatelé s touto rolí se stanou Správci místních počítačů na všech zařízeních s Windows 10, která jsou připojená k Azure Active Directory. Neposkytují možnost spravovat objekty zařízení v Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/groupSettings/Standard/Read | Číst základní vlastnosti pro nastavení skupiny |
> | Microsoft. Directory/groupSettingTemplates/Standard/Read | Čtení základních vlastností v šablonách nastavení skupiny |

## <a name="azure-devops-administrator"></a>Správce Azure DevOps

Uživatelé s touto rolí můžou spravovat zásady Azure DevOps a omezit tak nové vytváření organizací Azure DevOps na sadu konfigurovatelných uživatelů nebo skupin. Uživatelé v této roli mohou spravovat tuto zásadu prostřednictvím jakékoli organizace Azure DevOps, která je zajištěna organizací Azure AD společnosti. Tato role neuděluje žádná další oprávnění specifická pro Azure DevOps (například správce kolekce projektů) ve všech organizacích Azure DevOps zajištěných organizací Azure AD společnosti.

Všechny zásady Enterprise Azure DevOps můžou spravovat uživatelé v této roli.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. devOps/allEntities/allTasks | Čtení a konfigurace Azure DevOps |

## <a name="azure-information-protection-administrator"></a>Správce Azure Information Protection

Uživatelé s touto rolí mají všechna oprávnění ve službě Azure Information Protection. Tato role umožňuje konfigurovat popisky pro zásady Azure Information Protection, spravovat šablony ochrany a aktivovat ochranu. Tato role neuděluje žádná oprávnění v centru služby Identity Protection Center, Privileged Identity Management, monitor Microsoft 365 Service Health nebo v centru pro dodržování předpisů Office 365 Security &.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty Azure Information Protection. |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="b2c-ief-keyset-administrator"></a>B2C IEF, správce sady klíčů

Uživatel může vytvářet a spravovat klíče a tajné kódy zásad pro šifrování tokenů, signatury tokenů a šifrování a dešifrování deklarací identity. Přidáním nových klíčů do existujících kontejnerů klíčů může tento omezený správce podle potřeby přeměnit tajné klíče, aniž by to mělo vliv na stávající aplikace. Tento uživatel může zobrazit úplný obsah těchto tajných kódů a jejich datum vypršení platnosti i po jejich vytvoření.

> [!IMPORTANT]
> Tato role je citlivá. Role správce sady klíčů by měla být pečlivě auditována a přiřazena se opatrně během předprodukčních a produkčních verzí.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Číst a aktualizovat všechny vlastnosti zásad autorizace |

## <a name="b2c-ief-policy-administrator"></a>Správce zásad IEF B2C

Uživatelé v této roli mají možnost vytvářet, číst, aktualizovat a odstraňovat všechny vlastní zásady v Azure AD B2C a mají proto úplnou kontrolu nad architekturou prostředí identity v příslušné Azure AD B2C organizaci. Tím, že uživatel upraví zásady, může vytvořit přímou federaci s externími zprostředkovateli identity, měnit schéma adresáře, měnit obsah směřující do uživatele (HTML, CSS, JavaScript), měnit požadavky na dokončení ověřování, vytvářet nové uživatele, odesílat uživatelská data do externích systémů včetně úplných migrací a upravovat všechny informace o uživateli, včetně citlivých polí, jako jsou hesla a telefonní čísla. Naopak tato role nemůže měnit šifrovací klíče ani upravovat tajné klíče používané pro federaci v organizaci.

> [!IMPORTANT]
> Správce zásad IEF B2 je vysoce citlivá role, kterou je třeba pro organizace v produkčním prostředí přiřadit velmi omezenému základu. Aktivity těchto uživatelů by měly být pečlivě auditovány, zejména pro organizace v produkčním prostředí.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Číst a konfigurovat klíčové sady v Azure Active Directory B2C |

## <a name="billing-administrator"></a>Správce fakturace

Může dělat nákupy, spravovat předplatná, spravovat lístky žádostí o podporu a sledovat stav služeb.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Organization/Basic/Update | Aktualizace základních vlastností v organizaci |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Commerce. fakturace/allEntities/allTasks | Správa všech aspektů fakturace Office 365 |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="cloud-application-administrator"></a>Správce cloudové aplikace

Uživatelé v této roli mají stejná oprávnění jako role správce aplikace, kromě možnosti spravovat proxy aplikace. Tato role uděluje možnost vytvářet a spravovat všechny aspekty podnikových aplikací a registrací aplikací. Uživatelé přiřazení k této roli nebudou přidáni jako vlastníci při vytváření nových registrací aplikací nebo podnikových aplikací.

Tato role také uděluje možnost udělit souhlas pro delegovaná oprávnění a oprávnění aplikací s výjimkou oprávnění aplikace pro Microsoft Graph i Azure AD Graph.

> [!IMPORTANT]
> Tato výjimka znamená, že stále můžete vyjádřit souhlas s oprávněními aplikace pro _ostatní_ aplikace (například aplikace od jiných společností než Microsoft nebo aplikace, které jste zaregistrovali). Tato oprávnění si můžete přesto _vyžádat_ jako součást registrace aplikace, ale _udělení_ (tj. přijetí) vyžaduje oprávnění správce, jako je například globální správce.
>
>Tato role uděluje možnost spravovat přihlašovací údaje aplikací. Uživatelé přiřazení k této roli můžou do aplikace přidat přihlašovací údaje a pomocí těchto přihlašovacích údajů zosobnit identitu aplikace. Pokud má identita aplikace udělen přístup k prostředku, jako je třeba možnost vytvořit nebo aktualizovat uživatele nebo jiné objekty, může uživatel přiřazený k této roli provádět tyto akce při zosobnění aplikace. Tato schopnost zosobnit identitu aplikace může být zvýšením oprávnění, přes co může uživatel dělat prostřednictvím přiřazení rolí. Je důležité pochopit, že přiřazení uživatele k roli správce aplikace jim dává možnost zosobnit identitu aplikace.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/aplikace/vytvořit | Vytvořit všechny typy aplikací |
> | Microsoft. Directory/aplikace/odstranit | Odstranit všechny typy aplikací |
> | Microsoft. Directory/Applications/appRoles/Update | Aktualizuje vlastnost appRoles u všech typů aplikací. |
> | Microsoft. Directory/Applications/publikum/Update | Aktualizace vlastnosti cílové skupiny pro aplikace |
> | Microsoft. Directory/Applications/Authentication/Update | Aktualizovat ověřování u všech typů aplikací |
> | Microsoft. Directory/Applications/Basic/Update | Aktualizace základních vlastností pro aplikace |
> | Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizovat přihlašovací údaje aplikace |
> | Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizovat vlastníky aplikací |
> | Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizovat vystavená oprávnění a požadovaná oprávnění pro všechny typy aplikací |
> | Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizovat zásady aplikací |
> | Microsoft. Directory/Applications/ověření/aktualizace | Aktualizovat vlastnost applicationsverification |
> | Microsoft. Directory/aplikace/synchronizace/standardní/číst | Čtení nastavení zřizování přidružených k objektu aplikace |
> | Microsoft. Directory/applicationTemplates/instance | Vytvoření instance aplikací galerie z šablon aplikací |
> | Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti v protokolech auditu, včetně privilegovaných vlastností. |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Umožňuje vytvářet a odstraňovat oprávnění OAuth 2,0 a číst a aktualizovat všechny vlastnosti. |
> | Microsoft. Directory/applicationPolicies/Create | Vytvoření zásad použití |
> | Microsoft. Directory/applicationPolicies/DELETE | Odstranit zásady použití |
> | Microsoft. Directory/applicationPolicies/Standard/Read | Čtení standardních vlastností zásad použití |
> | Microsoft. Directory/applicationPolicies/Owners/Read | Čtení vlastníků u zásad použití |
> | Microsoft. Directory/applicationPolicies/policyAppliedTo/Read | Číst zásady použití pro seznam objektů |
> | Microsoft. Directory/applicationPolicies/Basic/Update | Aktualizace standardních vlastností zásad použití |
> | Microsoft. Directory/applicationPolicies/Owners/Update | Aktualizace vlastnosti Owner zásad použití |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování. |
> | Microsoft. Directory/servicePrincipals/Create | Vytvoření instančních objektů |
> | Microsoft. Directory/servicePrincipals/DELETE | Odstranění objektů služby |
> | Microsoft. Directory/servicePrincipals/Disable | Zakázat instanční objekty |
> | Microsoft. Directory/servicePrincipals/Enable | Povolení instančních objektů |
> | Microsoft. Directory/servicePrincipals/getPasswordSingleSignOnCredentials | Správa přihlašovacích údajů jednotného přihlašování k heslům u instančních objektů |
> | Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage | Správa tajných klíčů a přihlašovacích údajů pro zřizování aplikací |
> | Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Spusťte, restartujte a pozastavte úlohy synchronizace – zřizování aplikací. |
> | Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Vytváření a Správa synchronizace – úloh a schémat pro zřizování aplikací |
> | Microsoft. Directory/servicePrincipals/managePasswordSingleSignOnCredentials | Číst přihlašovací údaje jednotného přihlašování hesla u instančních objektů |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Application-admin | Udělení souhlasu pro oprávnění aplikace a delegovaná oprávnění jménem libovolného uživatele nebo všech uživatelů, s výjimkou oprávnění aplikace pro Microsoft Graph a Azure AD Graph |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizovat přiřazení rolí instančního objektu |
> | Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizovat vlastnosti cílové skupiny u instančních objektů |
> | Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizovat vlastnosti ověřování u instančních objektů |
> | Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizace základních vlastností u instančních objektů |
> | Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizovat přihlašovací údaje objektů služby |
> | Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizujte vlastníky instančních objektů. |
> | Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizovat oprávnění instančních objektů |
> | Microsoft. Directory/servicePrincipals/policies/Update | Aktualizovat zásady instančních objektů |
> | Microsoft. Directory/servicePrincipals/tag/Update | Aktualizace vlastnosti značky pro instanční objekty |
> | Microsoft. Directory/servicePrincipals/Synchronization/Standard/Read | Čtení nastavení zřizování přidružených k vašemu instančnímu objektu |
> | Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti v sestavách přihlášení, včetně privilegovaných vlastností. |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="cloud-device-administrator"></a>Správce cloudového zařízení

Uživatelé v této roli můžou povolit, zakázat a odstranit zařízení ve službě Azure AD a číst klíče Windows 10 BitLocker (pokud jsou k dispozici) v Azure Portal. Role neuděluje oprávnění ke správě dalších vlastností v zařízení.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti v protokolech auditu, včetně privilegovaných vlastností. |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Číst metadata a klíč nástroje BitLocker na zařízeních |
> | Microsoft. Directory/Devices/DELETE | Odstranění zařízení ze služby Azure AD |
> | Microsoft. Directory/Devices/Disable | Zakázat zařízení ve službě Azure AD |
> | Microsoft. Directory/Devices/Enable | Povolení zařízení ve službě Azure AD |
> | Microsoft. Directory/Devices/extensionAttributes/Update | Aktualizuje všechny hodnoty pro vlastnost Devices. extensionAttributes. |
> | Microsoft. Directory/deviceManagementPolicies/Standard/Read | Číst standardní vlastnosti v zásadách aplikací pro správu zařízení |
> | Microsoft. Directory/deviceManagementPolicies/Basic/Update | Aktualizace základních vlastností v zásadách aplikací pro správu zařízení |
> | Microsoft. Directory/deviceRegistrationPolicy/Standard/Read | Číst standardní vlastnosti v zásadách registrace zařízení |
> | Microsoft. Directory/deviceRegistrationPolicy/Basic/Update | Aktualizace základních vlastností v zásadách registrace zařízení |
> | Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti v sestavách přihlášení, včetně privilegovaných vlastností. |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |

## <a name="compliance-administrator"></a>Správce dodržování předpisů

Uživatelé s touto rolí mají oprávnění ke správě funkcí souvisejících s dodržováním předpisů v centru pro Microsoft 365 dodržování předpisů, Microsoft 365 centrum pro správu, Azure a zabezpečení Office 365 v centru dodržování předpisů &. Assignees může také spravovat všechny funkce v centru pro správu Exchange a týmy & centra pro správu Skypu pro firmy a vytvářet lístky podpory pro Azure a Microsoft 365. Další informace najdete v informacích [o Microsoft 365 rolích správce](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

V | Může
----- | ----------
[Centrum kompatibility Microsoft 365](https://protection.office.com) | Chraňte a spravujte data vaší organizace napříč Microsoft 365 službami<br>Spravovat výstrahy dodržování předpisů
[Správce dodržování předpisů](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Sledovat, přiřazovat a ověřovat aktivity dodržování předpisů právními předpisy vaší organizace
[Office 365 Security & – centrum dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad správného řízení dat<br>Provádět právní a vyšetřovací data<br>Správa požadavku subjektu dat<br><br>Tato role má stejná oprávnění jako [role správce dodržování předpisů](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) v sadě Office 365 Security & řízení přístupu na základě role v centru kompatibility.
[Intune](/intune/role-based-access-control) | Zobrazit všechna data auditu Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.<br>Může vytvářet a upravovat zásady souborů a umožňovat akce zásad správného řízení souborů.<br>Může zobrazit všechny předdefinované sestavy v části Správa dat

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Čtení všech vlastností v Azure AD – Správa nároků |
> | Microsoft. Office 365. complianceManager/allEntities/allTasks | Správa všech aspektů správce dodržování předpisů pro Office 365 |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="compliance-data-administrator"></a>Správce dat dodržování předpisů

Uživatelé s touto rolí mají oprávnění sledovat data v Microsoft 365 centrum dodržování předpisů, Microsoft 365 centrum pro správu a Azure. Uživatelé také mohou sledovat data o dodržování předpisů v centru pro správu Exchange, správců dodržování předpisů a týmech & centru pro správu Skypu pro firmy a vytvářet lístky podpory pro Azure a Microsoft 365. [Tato dokumentace](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) obsahuje podrobnosti o rozdílech mezi správcem dodržování předpisů a správcem dat dodržování předpisů.

V | Může
----- | ----------
[Centrum kompatibility Microsoft 365](https://protection.office.com) | Monitorování zásad souvisejících s dodržováním předpisů napříč Microsoft 365 službami<br>Spravovat výstrahy dodržování předpisů
[Správce dodržování předpisů](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Sledovat, přiřazovat a ověřovat aktivity dodržování předpisů právními předpisy vaší organizace
[Office 365 Security & – centrum dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Správa zásad správného řízení dat<br>Provádět právní a vyšetřovací data<br>Správa požadavku subjektu dat<br><br>Tato role má stejná oprávnění jako [role správce dat dodržování předpisů](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) v sadě Office 365 Security & řízení přístupu na základě role v centru dodržování předpisů.
[Intune](/intune/role-based-access-control) | Zobrazit všechna data auditu Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.<br>Může vytvářet a upravovat zásady souborů a umožňovat akce zásad správného řízení souborů.<br>Může zobrazit všechny předdefinované sestavy v části Správa dat

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft Cloud App Security |
> | Microsoft. Azure. informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty Azure Information Protection. |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. complianceManager/allEntities/allTasks | Správa všech aspektů správce dodržování předpisů pro Office 365 |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="conditional-access-administrator"></a>Správce podmíněného přístupu

Uživatelé s touto rolí mají možnost spravovat Azure Active Directory nastavení podmíněného přístupu.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/conditionalAccessPolicies/Create | Vytvoření zásad podmíněného přístupu |
> | Microsoft. Directory/conditionalAccessPolicies/DELETE | Odstranění zásad podmíněného přístupu |
> | Microsoft. Directory/conditionalAccessPolicies/Standard/Read | Číst podmíněný přístup pro zásady |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Read | Přečtěte si vlastníky zásad podmíněného přístupu. |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Přečtěte si vlastnost "použito na" pro zásady podmíněného přístupu. |
> | Microsoft. Directory/conditionalAccessPolicies/Basic/Update | Aktualizace základních vlastností pro zásady podmíněného přístupu |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Update | Aktualizace vlastníků pro zásady podmíněného přístupu |
> | Microsoft. Directory/conditionalAccessPolicies/tenantDefault/Update | Aktualizace výchozího tenanta pro zásady podmíněného přístupu |
> | Microsoft. Directory/crossTenantAccessPolicies/Create | Vytvoření zásad pro přístup mezi klienty |
> | Microsoft. Directory/crossTenantAccessPolicies/DELETE | Odstranění zásad pro přístup mezi klienty |
> | Microsoft. Directory/crossTenantAccessPolicies/Standard/Read | Čtení základních vlastností zásad přístupu mezi klienty |
> | Microsoft. Directory/crossTenantAccessPolicies/Owners/Read | Čtení vlastníků pro zásady přístupu mezi klienty |
> | Microsoft. Directory/crossTenantAccessPolicies/policyAppliedTo/Read | Přečtěte si vlastnost policyAppliedTo pro zásady přístupu mezi klienty. |
> | Microsoft. Directory/crossTenantAccessPolicies/Basic/Update | Aktualizace základních vlastností zásad přístupu mezi klienty |
> | Microsoft. Directory/crossTenantAccessPolicies/Owners/Update | Aktualizace vlastníků pro zásady přístupu mezi klienty |
> | Microsoft. Directory/crossTenantAccessPolicies/tenantDefault/Update | Aktualizace výchozího tenanta pro zásady přístupu mezi klienty |

## <a name="customer-lockbox-access-approver"></a>Schvalovatel přístupu k bezpečnostnímu modulu zákazníka

Spravuje [žádosti Customer Lockbox](/office365/admin/manage/customer-lockbox-requests) ve vaší organizaci. Přijímají e-mailová oznámení týkající se Customer Lockbox požadavků a můžou schvalovat a zamítat žádosti z centra pro správu Microsoft 365. Také je možné zapnout nebo vypnout funkci Customer Lockbox. Pouze globální Správci mohou resetovat hesla uživatelů přiřazených k této roli.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. bezpečnostní modul/allEntities/allTasks | Umožňuje spravovat všechny aspekty Customer Lockbox. |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="desktop-analytics-administrator"></a>Správce Desktop Analytics

Uživatelé v této roli můžou spravovat služby zásad & pro stolní počítače a přizpůsobení Office. Pro Desktop Analytics to zahrnuje možnost Zobrazit inventář assetů, vytvářet plány nasazení, zobrazovat stav nasazení a stavu. V případě služby Office Customization & Policy tato role umožňuje uživatelům spravovat zásady Office.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. desktopAnalytics/allEntities/allTasks | Správa všech aspektů Desktop Analytics |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="directory-readers"></a>Čtečky adresářů

Uživatelé v této roli mohou číst základní informace o adresáři. Tato role by se měla použít pro:

* Udělení přístupu ke čtení konkrétní sady uživatelů typu host místo udělení oprávnění všem uživatelům typu Host.
* Udělení konkrétní sady uživatelů bez oprávnění správce přístup k Azure Portal, když je možnost omezit přístup k portálu Azure AD jenom na správce, nastavená na Ano.
* Udělení instančních objektů přístup k adresáři, kde Directory. Read. All není možnost.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/administrativeUnits/Standard/Read | Číst základní vlastnosti pro jednotky pro správu |
> | Microsoft. Directory/administrativeUnits/Members/Read | Čtení členů jednotek pro správu |
> | Microsoft. Directory/Applications/Standard/Read | Čtení standardních vlastností aplikací |
> | Microsoft. Directory/aplikace/vlastníci/čtení | Čtení vlastníků aplikací |
> | Microsoft. Directory/aplikace/zásady/číst | Čtení zásad aplikací |
> | Microsoft. Directory/Contacts/Standard/Read | Číst základní vlastnosti kontaktů v Azure AD |
> | Microsoft. Directory/Contacts/memberOf/Read | Číst členství ve skupině pro všechny kontakty ve službě Azure AD |
> | Microsoft. Directory/Contracts/Standard/Read | Čtení základních vlastností na kontraktech partnerů |
> | Microsoft. Directory/zařízení/standardní/číst | Číst základní vlastnosti na zařízeních |
> | Microsoft. Directory/zařízení/memberOf/číst | Čtení členství v zařízeních |
> | Microsoft. Directory/Devices/registeredOwners/Read | Čtení registrovaných vlastníků zařízení |
> | Microsoft. Directory/Devices/registeredUsers/Read | Čtení registrovaných uživatelů zařízení |
> | Microsoft. Directory/directoryRoles/Standard/Read | Aktualizace základních vlastností v rolích Azure AD |
> | Microsoft. Directory/directoryRoles/eligibleMembers/Read | Čtení oprávněných členů rolí Azure AD |
> | Microsoft. Directory/directoryRoles/Members/Read | Čtení všech členů rolí Azure AD |
> | Microsoft. Directory/domény/standardní/číst | Číst základní vlastnosti v doménách |
> | Microsoft. Directory/Groups/Standard/Read | Číst základní vlastnosti ve skupinách |
> | Microsoft. Directory/Groups/appRoleAssignments/Read | Čtení rolí aplikace přiřazení skupin |
> | Microsoft. Directory/Groups/memberOf/Read | Čtení skupin, které skupina je členem v Azure AD |
> | Microsoft. Directory/Groups/Members/Read | Čtení členů skupin |
> | Microsoft. Directory/Groups/Owners/Read | Čtení vlastníků skupin |
> | Microsoft. Directory/Groups/Settings/Read | Čtení nastavení skupin |
> | Microsoft. Directory/groupSettings/Standard/Read | Číst základní vlastnosti pro nastavení skupiny |
> | Microsoft. Directory/groupSettingTemplates/Standard/Read | Čtení základních vlastností v šablonách nastavení skupiny |
> | Microsoft. Directory/oAuth2PermissionGrants/Standard/Read | Číst základní vlastnosti pro udělení oprávnění OAuth 2,0 |
> | Microsoft. Directory/Organization/Standard/číst | Čtení základních vlastností v organizaci |
> | Microsoft. Directory/Organization/trustedCAsForPasswordlessAuth/Read | Čtení důvěryhodných certifikačních autorit pro ověřování neheslem |
> | Microsoft. Directory/applicationPolicies/Standard/Read | Čtení standardních vlastností zásad použití |
> | Microsoft. Directory/roleAssignments/Standard/Read | Číst základní vlastnosti přiřazení rolí |
> | Microsoft. Directory/roleDefinitions/Standard/Read | Číst základní vlastnosti definic rolí |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Read | Čtení přiřazení rolí instančního objektu |
> | Microsoft. Directory/servicePrincipals/appRoleAssignments/Read | Čtení přiřazení rolí přiřazených k instančním objektům |
> | Microsoft. Directory/servicePrincipals/Standard/Read | Čtení základních vlastností instančních objektů |
> | Microsoft. Directory/servicePrincipals/memberOf/Read | Přečtěte si členství ve skupinách u instančních objektů. |
> | Microsoft. Directory/servicePrincipals/oAuth2PermissionGrants/Read | Oprávnění číst delegovaná oprávnění pro instanční objekty |
> | Microsoft. Directory/servicePrincipals/Owners/Read | Čtení vlastníků objektů služby |
> | Microsoft. Directory/servicePrincipals/ownedObjects/Read | Číst vlastněné objekty instančních objektů |
> | Microsoft. Directory/servicePrincipals/policies/Read | Čtení zásad objektů služby |
> | Microsoft. Directory/subscribedSkus/Standard/Read | Číst základní vlastnosti v předplatných |
> | Microsoft. Directory/uživatelé/standardní/číst | Číst základní vlastnosti uživatelů |
> | Microsoft. Directory/Users/appRoleAssignments/Read | Čtení přiřazení rolí aplikace uživatelům |
> | Microsoft. Directory/Users/directReports/Read | Čtení přímých sestav pro uživatele |
> | Microsoft. Directory/uživatelé/Správce/číst | Správce čtení uživatelů |
> | Microsoft. Directory/uživatelé/memberOf/číst | Číst členství uživatelů ve skupině |
> | Microsoft. Directory/Users/oAuth2PermissionGrants/Read | Číst delegovaná oprávnění pro uživatele |
> | Microsoft. Directory/Users/ownedDevices/Read | Čtení zařízení vlastněných uživateli |
> | Microsoft. Directory/Users/ownedObjects/Read | Číst vlastněné objekty uživatelů |
> | Microsoft. Directory/Users/registeredDevices/Read | Čtení registrovaných zařízení uživatelů |

## <a name="directory-synchronization-accounts"></a>Účty synchronizace adresářů

Nepoužívat. Tato role je automaticky přiřazena ke službě Azure AD Connect a není určena ani podporována pro jiné použití.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/aplikace/vytvořit | Vytvořit všechny typy aplikací |
> | Microsoft. Directory/aplikace/odstranit | Odstranit všechny typy aplikací |
> | Microsoft. Directory/Applications/appRoles/Update | Aktualizuje vlastnost appRoles u všech typů aplikací. |
> | Microsoft. Directory/Applications/publikum/Update | Aktualizace vlastnosti cílové skupiny pro aplikace |
> | Microsoft. Directory/Applications/Authentication/Update | Aktualizovat ověřování u všech typů aplikací |
> | Microsoft. Directory/Applications/Basic/Update | Aktualizace základních vlastností pro aplikace |
> | Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizovat přihlašovací údaje aplikace |
> | Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizovat vlastníky aplikací |
> | Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizovat vystavená oprávnění a požadovaná oprávnění pro všechny typy aplikací |
> | Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizovat zásady aplikací |
> | Microsoft. Directory/Organization/dirSync/Update | Aktualizovat vlastnost synchronizace adresáře organizace |
> | Microsoft. Directory/policies/Create | Vytvoření zásad ve službě Azure AD |
> | Microsoft. Directory/policies/DELETE | Odstranění zásad ve službě Azure AD |
> | Microsoft. Directory/policies/Standard/Read | Číst základní vlastnosti zásad |
> | Microsoft. Directory/policies/Owners/Read | Čtení vlastníků zásad |
> | Microsoft. Directory/policies/policyAppliedTo/Read | Číst zásady. vlastnost policyAppliedTo |
> | Microsoft. Directory/policies/Basic/Update | Aktualizace základních vlastností v zásadách |
> | Microsoft. Directory/policies/Owners/Update | Aktualizovat vlastníky zásad |
> | Microsoft. Directory/policies/tenantDefault/Update | Aktualizace výchozích zásad organizace |
> | Microsoft. Directory/servicePrincipals/Create | Vytvoření instančních objektů |
> | Microsoft. Directory/servicePrincipals/DELETE | Odstranění objektů služby |
> | Microsoft. Directory/servicePrincipals/Enable | Povolení instančních objektů |
> | Microsoft. Directory/servicePrincipals/Disable | Zakázat instanční objekty |
> | Microsoft. Directory/servicePrincipals/getPasswordSingleSignOnCredentials | Správa přihlašovacích údajů jednotného přihlašování k heslům u instančních objektů |
> | Microsoft. Directory/servicePrincipals/managePasswordSingleSignOnCredentials | Číst přihlašovací údaje jednotného přihlašování hesla u instančních objektů |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Read | Čtení přiřazení rolí instančního objektu |
> | Microsoft. Directory/servicePrincipals/appRoleAssignments/Read | Čtení přiřazení rolí přiřazených k instančním objektům |
> | Microsoft. Directory/servicePrincipals/Standard/Read | Čtení základních vlastností instančních objektů |
> | Microsoft. Directory/servicePrincipals/memberOf/Read | Přečtěte si členství ve skupinách u instančních objektů. |
> | Microsoft. Directory/servicePrincipals/oAuth2PermissionGrants/Read | Oprávnění číst delegovaná oprávnění pro instanční objekty |
> | Microsoft. Directory/servicePrincipals/Owners/Read | Čtení vlastníků objektů služby |
> | Microsoft. Directory/servicePrincipals/ownedObjects/Read | Číst vlastněné objekty instančních objektů |
> | Microsoft. Directory/servicePrincipals/policies/Read | Čtení zásad objektů služby |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizovat přiřazení rolí instančního objektu |
> | Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizovat vlastnosti cílové skupiny u instančních objektů |
> | Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizovat vlastnosti ověřování u instančních objektů |
> | Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizace základních vlastností u instančních objektů |
> | Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizovat přihlašovací údaje objektů služby |
> | Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizujte vlastníky instančních objektů. |
> | Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizovat oprávnění instančních objektů |
> | Microsoft. Directory/servicePrincipals/policies/Update | Aktualizovat zásady instančních objektů |
> | Microsoft. Directory/servicePrincipals/tag/Update | Aktualizace vlastnosti značky pro instanční objekty |

## <a name="directory-writers"></a>Zapisovače adresářů

Uživatelé v této roli mohou číst a aktualizovat základní informace o uživatelích, skupinách a instančních objektech. Tuto roli přiřaďte pouze aplikacím, které nepodporují [rámec souhlasu](../develop/quickstart-register-app.md). Neměl by být přiřazený žádnému uživateli.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Groups/assignLicense | Přiřazení licencí k produktu skupinám pro licencování na základě skupin |
> | Microsoft. Directory/Groups/Create | Vytváření skupin s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/reprocessLicenseAssignment | Opětovné zpracování přiřazení licencí pro licencování na základě skupin |
> | Microsoft. Directory/Groups/Basic/Update | Aktualizovat základní vlastnosti u skupin s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/klasifikací/Update | Aktualizovat vlastnost klasifikace skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/dynamicMembershipRule/Update | Aktualizovat dynamické pravidlo členství skupin s výjimkou skupin, které lze přiřadit k rolím |
> | Microsoft. Directory/Groups/groupType/Update | Aktualizace vlastnosti groupType pro skupinu |
> | Microsoft. Directory/Groups/Members/Update | Aktualizace členů skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/onPremWriteBack/Update | Aktualizace skupin Azure Active Directory pro zápis do místního prostředí s využitím Azure AD Connect |
> | Microsoft. Directory/Groups/Owners/Update | Aktualizace vlastníků skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/Settings/Update | Aktualizovat nastavení skupin |
> | Microsoft. Directory/Groups/Visibility/Update | Aktualizuje vlastnost viditelnost skupin. |
> | Microsoft. Directory/groupSettings/Create | Vytvoření nastavení skupin |
> | Microsoft. Directory/groupSettings/DELETE | Odstranění nastavení skupin |
> | Microsoft. Directory/groupSettings/Basic/Update | Aktualizovat základní vlastnosti pro nastavení skupiny |
> | Microsoft. Directory/oAuth2PermissionGrants/Create | Vytvoření udělení oprávnění OAuth 2,0 |
> | Microsoft. Directory/oAuth2PermissionGrants/Basic/Update | Aktualizovat udělení oprávnění OAuth 2,0 |
> | Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage | Správa tajných klíčů a přihlašovacích údajů pro zřizování aplikací |
> | Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Spusťte, restartujte a pozastavte úlohy synchronizace – zřizování aplikací. |
> | Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Vytváření a Správa synchronizace – úloh a schémat pro zřizování aplikací |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-All-Application-Permissions | Udělení instančního objektu s přímým přístupem k datům skupiny |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizovat přiřazení rolí instančního objektu |
> | Microsoft. Directory/Users/assignLicense | Správa uživatelských licencí |
> | Microsoft. Directory/uživatelé/vytvořit | Přidání uživatelů |
> | Microsoft. Directory/Users/Disable | Zakázat uživatele |
> | Microsoft. Directory/uživatelé/povolit | Povolit uživatele |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Vynutit odhlášení pomocí neověřování tokenů aktualizace uživatelů |
> | Microsoft. Directory/Users/reprocessLicenseAssignment | Znovu zpracovat přiřazení licencí pro uživatele |
> | Microsoft. Directory/uživatelé/Basic/Update | Aktualizace základních vlastností u uživatelů |
> | Microsoft. Directory/uživatelé/správce/aktualizace | Správce aktualizací pro uživatele |
> | Microsoft. Directory/uživatelé/userPrincipalName/Update | Aktualizovat hlavní název uživatele uživatelů |

## <a name="domain-name-administrator"></a>Správce názvů domén

Uživatelé s touto rolí můžou spravovat názvy domén (čtení, přidávání, ověřování, aktualizace a odstraňování). Můžou také číst informace o uživatelích, skupinách a aplikacích, protože tyto objekty mají závislosti domény. Pro místní prostředí můžou uživatelé s touto rolí konfigurovat názvy domén pro federaci, aby se přidružení uživatelé vždycky místně ověřili místně. Tito uživatelé se pak mohou přihlašovat ke službám založeným na službě Azure AD s místními hesly prostřednictvím jednotného přihlašování. Nastavení federace je třeba synchronizovat prostřednictvím Azure AD Connect, takže uživatelé mají také oprávnění ke správě Azure AD Connect.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/domény/allProperties/allTasks | Vytváření a odstraňování domén a čtení a aktualizace všech vlastností |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |

## <a name="dynamics-365-administrator"></a>Správce Dynamics 365

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Dynamics 365 online, pokud je služba k dispozici, a také možnost spravovat lístky podpory a monitorovat stav služby. Další informace najdete v [části použití role správce služby ke správě vaší organizace Azure AD](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako správce služeb Dynamics 365. Ve [Azure Portal](https://portal.azure.com)je to "Dynamics 365 Administrator".

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. dynamics365/allEntities/allTasks | Správa všech aspektů Dynamics 365 |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="exchange-administrator"></a>Správce Exchange

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Exchange Online, pokud je tato služba k dispozici. Má také možnost vytvářet a spravovat všechny Microsoft 365 skupiny, spravovat lístky podpory a monitorovat stav služby. Další informace [o Microsoft 365 rolích správce](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako "Správce služby Exchange". Je to "správce Exchange" v [Azure Portal](https://portal.azure.com). Je to "Exchange Online Administrator" v [centru pro správu Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Čtení skrytých členů skupiny |
> | Microsoft. Directory/groups. Unified/Create | Vytvoření skupin Microsoft 365 s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/groups. Unified/DELETE | Odstranit skupiny Microsoft 365 s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Directory/groups. Unified/Restore | Obnovit Microsoft 365 skupiny |
> | Microsoft. Directory/groups. Unified/Basic/Update | Aktualizace základních vlastností u Microsoft 365 skupin s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Directory/groups. Unified/Members/Update | Aktualizace členů skupin Microsoft 365 s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Directory/groups. Unified/Owners/Update | Aktualizace vlastníků skupin Microsoft 365 s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. Exchange/allEntities/Basic/allTasks | Správa všech aspektů Exchange Online |
> | Microsoft. Office 365. Network/Performance/allProperties/Read | Čtení všech vlastností výkonu sítě v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Čtení sestav využití Office 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="exchange-recipient-administrator"></a>Správce příjemce Exchange

Uživatelé s touto rolí mají oprávnění ke čtení pro příjemce a přístup pro zápis k atributům příjemců v Exchangi Online. Další informace o [příjemce Exchange](/exchange/recipients/recipients).

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. Exchange/allRecipients/allProperties/allTasks | Vytváření a odstraňování všech příjemců a čtení a aktualizace všech vlastností příjemců v Exchangi Online |
> | Microsoft. Office 365. Exchange/messageTracking/allProperties/allTasks | Správa všech úloh ve sledování zpráv v Exchangi Online |
> | Microsoft. Office 365. Exchange/Migration/allProperties/allTasks | Spravovat všechny úlohy související s migrací příjemců v Exchangi Online |

## <a name="external-id-user-flow-administrator"></a>Správce toku externího ID uživatele

Uživatelé s touto rolí můžou vytvářet a spravovat toky uživatelů (označované taky jako předdefinované zásady) v Azure Portal. Tito uživatelé mohou přizpůsobit obsah HTML/CSS/JavaScript, měnit požadavky MFA, vybírat deklarace identity v tokenu, spravovat konektory rozhraní API a konfigurovat nastavení relace pro všechny toky uživatelů v organizaci Azure AD. Na druhé straně tato role nezahrnuje možnost kontrolovat uživatelská data ani provádět změny atributů, které jsou součástí schématu organizace. Změny zásad architektury identity Framework (označované také jako vlastní zásady) jsou také mimo rozsah této role.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/b2cUserFlow/allProperties/allTasks | Čtení a konfigurace atributů uživatele v Azure Active Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>Správce atributů toku uživatele externího ID

Uživatelé s touto rolí můžou přidávat nebo odstraňovat vlastní atributy dostupné všem uživatelským tokům v organizaci Azure AD. Uživatelé s touto rolí mohou například měnit nebo přidávat nové prvky do schématu koncového uživatele a ovlivnit chování všech toků uživatelů a nepřímo způsobit změny v tom, jaká data mohou být požádána o koncové uživatele a která jsou nakonec odeslána jako deklarace do aplikací. Tato role nemůže upravovat toky uživatelů.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/b2cUserAttribute/allProperties/allTasks | Čtení a konfigurace vlastních zásad v Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Správce externích zprostředkovatelů identity

Tento správce spravuje federace mezi organizacemi Azure AD a externími zprostředkovateli identity. S touto rolí můžou uživatelé přidávat nové zprostředkovatele identity a konfigurovat všechna dostupná nastavení (například cestu pro ověřování, ID služby, přiřazené kontejnery klíčů). Tento uživatel může organizaci Azure AD povolit, aby důvěřoval ověřování od externích zprostředkovatelů identity. Výsledný dopad na činnost koncového uživatele závisí na typu organizace:

* Organizace Azure AD pro zaměstnance a partnery: Přidání federace (např. Gmail) okamžitě ovlivní všechny pozvánky hostů, které ještě nebyly uplatněny. Viz téma [Přidání Google jako zprostředkovatele identity pro uživatele typu Host B2B](../external-identities/google-federation.md).
* Azure Active Directory B2C organizace: Přidání federace (například s Facebookem nebo s jinou organizací Azure AD) okamžitě nemá vliv na toky koncového uživatele, dokud se poskytovatel identity nepřidá jako možnost v toku uživatele (označované také jako předdefinované zásady). Příklad najdete v tématu [konfigurace účet Microsoft jako zprostředkovatele identity](../../active-directory-b2c/identity-provider-microsoft-account.md) . Pro změnu toků uživatelů se vyžaduje omezená role "správce toku uživatelů B2C".

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/identityProviders/allProperties/allTasks | Čtení a konfigurace zprostředkovatelů identity v Azure Active Directory B2C |

## <a name="global-administrator"></a>Globální správce

Uživatelé s touto rolí mají přístup ke všem funkcím pro správu v Azure Active Directory a také službám, které používají Azure Active Directory identity, jako je Microsoft 365 Security Center, Microsoft 365 Center pro dodržování předpisů, Exchange Online, SharePoint Online a Online Skype pro firmy. Globální správci navíc můžou [zvýšit úroveň přístupu](../../role-based-access-control/elevate-access-global-admin.md) ke správě všech předplatných Azure a skupin pro správu. Díky tomu můžou globální správci získat úplný přístup ke všem prostředkům Azure pomocí příslušného tenanta Azure AD. Osoba, která se zaregistruje do organizace Azure AD, se stal globálním správcem. Ve vaší společnosti může být víc než jeden globální správce. Globální správci můžou resetovat heslo pro každého uživatele a všechny ostatní správce.

> [!NOTE]
> Osvědčeným postupem je, že společnost Microsoft doporučuje přiřadit roli globálního správce k méně než pěti lidem ve vaší organizaci. Další informace najdete v tématu [osvědčené postupy pro role Azure AD](best-practices.md).

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/accessReviews/allProperties/allTasks | Vytváření a odstraňování kontrol přístupu a čtení a aktualizace všech vlastností kontrol přístupu v Azure AD |
> | Microsoft. Directory/administrativeUnits/allProperties/allTasks | Vytváření a Správa jednotek pro správu (včetně členů) |
> | Microsoft. Directory/Applications/allProperties/allTasks | Vytváření a odstraňování aplikací a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/aplikace/synchronizace/standardní/číst | Čtení nastavení zřizování přidružených k objektu aplikace |
> | Microsoft. Directory/applicationTemplates/instance | Vytvoření instance aplikací galerie z šablon aplikací |
> | Microsoft. Directory/appRoleAssignments/allProperties/allTasks | Vytváření a odstraňování appRoleAssignments a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti v protokolech auditu, včetně privilegovaných vlastností. |
> | Microsoft. Directory/authorizationPolicy/allProperties/allTasks | Správa všech aspektů zásad autorizace |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Číst metadata a klíč nástroje BitLocker na zařízeních |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft Cloud App Security |
> | Microsoft. Directory/konektory/vytvořit | Vytváření konektorů proxy aplikací |
> | Microsoft. Directory/Connectors/allProperties/Read | Načte všechny vlastnosti konektorů proxy aplikací. |
> | Microsoft. Directory/connectorGroups/Create | Vytvořit skupiny konektorů proxy aplikací |
> | Microsoft. Directory/connectorGroups/DELETE | Odstranit skupiny konektorů proxy aplikací |
> | Microsoft. Directory/connectorGroups/allProperties/Read | Načte všechny vlastnosti skupin konektorů proxy aplikací. |
> | Microsoft. Directory/connectorGroups/allProperties/Update | Aktualizovat všechny vlastnosti skupin konektorů proxy aplikací |
> | Microsoft. Directory/Contacts/allProperties/allTasks | Vytváření a odstraňování kontaktů a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/Contracts/allProperties/allTasks | Vytváření a odstraňování partnerských smluv a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/Devices/allProperties/allTasks | Vytváření a odstraňování zařízení a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/deviceManagementPolicies/Standard/Read | Číst standardní vlastnosti v zásadách aplikací pro správu zařízení |
> | Microsoft. Directory/deviceManagementPolicies/Basic/Update | Aktualizace základních vlastností v zásadách aplikací pro správu zařízení |
> | Microsoft. Directory/deviceRegistrationPolicy/Standard/Read | Číst standardní vlastnosti v zásadách registrace zařízení |
> | Microsoft. Directory/deviceRegistrationPolicy/Basic/Update | Aktualizace základních vlastností v zásadách registrace zařízení |
> | Microsoft. Directory/directoryRoles/allProperties/allTasks | Vytváření a odstraňování rolí adresáře a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/directoryRoleTemplates/allProperties/allTasks | Vytváření a odstraňování šablon rolí Azure AD a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/domény/allProperties/allTasks | Vytváření a odstraňování domén a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/entitlementManagement/allProperties/allTasks | Vytváření a odstraňování prostředků a čtení a aktualizace všech vlastností v Azure AD – Správa nároků |
> | Microsoft. Directory/Groups/allProperties/allTasks | Vytváření a odstraňování skupin a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/groupsAssignableToRoles/Create | Vytváření skupin s možností přiřazení rolí |
> | Microsoft. Directory/groupsAssignableToRoles/DELETE | Odstranit roli – přiřaditelné skupiny |
> | Microsoft. Directory/groupsAssignableToRoles/Restore | Obnovit roli – přiřaditelné skupiny |
> | Microsoft. Directory/groupsAssignableToRoles/allProperties/Update | Aktualizace role – přiřaditelné skupiny |
> | Microsoft. Directory/groupSettings/allProperties/allTasks | Vytvoření a odstranění nastavení skupiny a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/groupSettingTemplates/allProperties/allTasks | Vytvoření a odstranění šablon nastavení skupiny a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/identityProtection/allProperties/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure AD Identity Protection |
> | Microsoft. Directory/loginOrganizationBranding/allProperties/allTasks | Vytváření a odstraňování loginTenantBranding a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Umožňuje vytvářet a odstraňovat oprávnění OAuth 2,0 a číst a aktualizovat všechny vlastnosti. |
> | Microsoft. Directory/Organization/allProperties/allTasks | Vytváření a odstraňování organizací a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/policies/allProperties/allTasks | Vytváření a odstraňování zásad a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/conditionalAccessPolicies/allProperties/allTasks | Správa všech vlastností zásad podmíněného přístupu |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/Read | Čtení všech prostředků v Privileged Identity Management |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování. |
> | Microsoft. Directory/roleAssignments/allProperties/allTasks | Vytváření a odstraňování přiřazení rolí a čtení a aktualizace vlastností přiřazení rolí |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Vytváření a odstraňování definic rolí a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | Vytváření a odstraňování scopedRoleMemberships a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/serviceAction/activateService | Může pro službu provést akci "aktivovat službu". |
> | Microsoft. Directory/serviceAction/disableDirectoryFeature | Může provést akci služby zakázat funkci adresáře. |
> | Microsoft. Directory/serviceAction/enableDirectoryFeature | Může provést akci služby povolit funkci adresáře. |
> | Microsoft. Directory/serviceAction/getAvailableExtentionProperties | Může provádět akci služby getAvailableExtentionProperties. |
> | Microsoft. Directory/servicePrincipals/allProperties/allTasks | Vytváření a odstraňování objektů služby a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Company-Admin | Udělení souhlasu pro všechna oprávnění k jakékoli aplikaci |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-All-Application-Permissions | Udělení instančního objektu s přímým přístupem k datům skupiny |
> | Microsoft. Directory/servicePrincipals/Synchronization/Standard/Read | Čtení nastavení zřizování přidružených k vašemu instančnímu objektu |
> | Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti v sestavách přihlášení, včetně privilegovaných vlastností. |
> | Microsoft. Directory/subscribedSkus/allProperties/allTasks | Nákup a Správa předplatných a odstraňování předplatných |
> | Microsoft. Directory/Users/allProperties/allTasks | Vytváření a odstraňování uživatelů a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/permissionGrantPolicies/Create | Vytvoření zásad udělení oprávnění |
> | Microsoft. Directory/permissionGrantPolicies/DELETE | Odstranění zásad udělení oprávnění |
> | Microsoft. Directory/permissionGrantPolicies/Standard/Read | Načte standardní vlastnosti zásad udělení oprávnění. |
> | Microsoft. Directory/permissionGrantPolicies/Basic/Update | Aktualizace základních vlastností zásad udělení oprávnění |
> | Microsoft. Directory/servicePrincipalCreationPolicies/Create | Vytvoření zásad pro vytvoření instančního objektu |
> | Microsoft. Directory/servicePrincipalCreationPolicies/DELETE | Odstranit zásady vytváření instančního objektu |
> | Microsoft. Directory/servicePrincipalCreationPolicies/Standard/Read | Načte standardní vlastnosti zásad pro vytvoření instančního objektu. |
> | Microsoft. Directory/servicePrincipalCreationPolicies/Basic/Update | Aktualizuje základní vlastnosti zásad pro vytvoření instančního objektu. |
> | Microsoft. Azure. advancedThreatProtection/allEntities/allTasks | Spravovat všechny aspekty rozšířené ochrany před internetovými útoky Azure |
> | Microsoft. Azure. informationProtection/allEntities/allTasks | Umožňuje spravovat všechny aspekty Azure Information Protection. |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Commerce. fakturace/allEntities/allTasks | Správa všech aspektů fakturace Office 365 |
> | Microsoft. dynamics365/allEntities/allTasks | Správa všech aspektů Dynamics 365 |
> | Microsoft. Flow/allEntities/allTasks | Správa všech aspektů Microsoft Power automatizuje |
> | Microsoft. Intune/allEntities/allTasks | Umožňuje spravovat všechny aspekty Microsoft Intune. |
> | Microsoft. Office 365. complianceManager/allEntities/allTasks | Správa všech aspektů správce dodržování předpisů pro Office 365 |
> | Microsoft. Office 365. desktopAnalytics/allEntities/allTasks | Správa všech aspektů Desktop Analytics |
> | Microsoft. Office 365. Exchange/allEntities/Basic/allTasks | Správa všech aspektů Exchange Online |
> | Microsoft. Office 365. bezpečnostní modul/allEntities/allTasks | Umožňuje spravovat všechny aspekty Customer Lockbox. |
> | Microsoft. Office 365. messageCenter/Messages/Read | Čtení zpráv v centru zpráv v centru pro správu Microsoft 365 s výjimkou zpráv zabezpečení |
> | Microsoft. Office 365. messageCenter/securityMessages/Read | Čtení zpráv zabezpečení v centru zpráv v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. Network/Performance/allProperties/Read | Čtení všech vlastností výkonu sítě v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. protectionCenter/allEntities/allProperties/allTasks | Správa všech aspektů Center zabezpečení a dodržování předpisů |
> | Microsoft. Office 365. Search/Content/Manage | Vytváření a odstraňování obsahu a čtení a aktualizace všech vlastností ve společnosti Microsoft Search |
> | Microsoft. Office 365. securityComplianceCenter/allEntities/allTasks | Vytváření a odstraňování všech prostředků a čtení a aktualizace standardních vlastností v centru Microsoft 365 Security and dodržování předpisů |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. sharePoint/allEntities/allTasks | Vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v SharePointu |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Čtení sestav využití Office 365 |
> | Microsoft. Office 365. userCommunication/allEntities/allTasks | Přečtěte si a aktualizujte viditelnost novinek zpráv |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |
> | Microsoft. powerApps/allEntities/allTasks | Správa všech aspektů Power Apps |
> | Microsoft. powerApps. powerBI/allEntities/allTasks | Umožňuje spravovat všechny aspekty Power BI. |
> | Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/allTasks | Správa všech aspektů programu Microsoft Defender pro koncový bod |

## <a name="global-reader"></a>Globální čtenář

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

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Applications/applicationProxy/Read | Číst všechny vlastnosti proxy aplikace |
> | Microsoft. Directory/aplikace/synchronizace/standardní/číst | Čtení nastavení zřizování přidružených k objektu aplikace |
> | Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti v protokolech auditu, včetně privilegovaných vlastností. |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Číst metadata a klíč nástroje BitLocker na zařízeních |
> | Microsoft. Directory/Connectors/allProperties/Read | Načte všechny vlastnosti konektorů proxy aplikací. |
> | Microsoft. Directory/connectorGroups/allProperties/Read | Načte všechny vlastnosti skupin konektorů proxy aplikací. |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Čtení všech vlastností v Azure AD – Správa nároků |
> | Microsoft. Directory/deviceManagementPolicies/Standard/Read | Číst standardní vlastnosti v zásadách aplikací pro správu zařízení |
> | Microsoft. Directory/deviceRegistrationPolicy/Standard/Read | Číst standardní vlastnosti v zásadách registrace zařízení |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Čtení skrytých členů skupiny |
> | Microsoft. Directory/policies/Standard/Read | Číst základní vlastnosti zásad |
> | Microsoft. Directory/policies/Owners/Read | Čtení vlastníků zásad |
> | Microsoft. Directory/policies/policyAppliedTo/Read | Číst zásady. vlastnost policyAppliedTo |
> | Microsoft. Directory/conditionalAccessPolicies/Standard/Read | Číst podmíněný přístup pro zásady |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Read | Přečtěte si vlastníky zásad podmíněného přístupu. |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Přečtěte si vlastnost "použito na" pro zásady podmíněného přístupu. |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování. |
> | Microsoft. Directory/servicePrincipals/Authentication/Read | Číst vlastnosti ověřování u instančních objektů |
> | Microsoft. Directory/servicePrincipals/Synchronization/Standard/Read | Čtení nastavení zřizování přidružených k vašemu instančnímu objektu |
> | Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti v sestavách přihlášení, včetně privilegovaných vlastností. |
> | Microsoft. Directory/Users/strongAuthentication/Read | Přečtěte si vlastnost silného ověřování pro uživatele. |
> | Microsoft. Commerce. fakturace/allEntities/čtení | Přečtěte si všechny prostředky fakturace Office 365. |
> | Microsoft. Office 365. Exchange/allEntities/Standard/Read | Čtení všech prostředků Exchange Online |
> | Microsoft. Office 365. messageCenter/Messages/Read | Čtení zpráv v centru zpráv v centru pro správu Microsoft 365 s výjimkou zpráv zabezpečení |
> | Microsoft. Office 365. messageCenter/securityMessages/Read | Čtení zpráv zabezpečení v centru zpráv v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. Network/Performance/allProperties/Read | Čtení všech vlastností výkonu sítě v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. protectionCenter/allEntities/allProperties/Read | Čtení všech vlastností v centrech zabezpečení a dodržování předpisů |
> | Microsoft. Office 365. securityComplianceCenter/allEntities/Read | Číst standardní vlastnosti v Microsoft 365 zabezpečení a centrum dodržování předpisů |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Čtení sestav využití Office 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="groups-administrator"></a>Správce skupin

Uživatelé v této roli můžou vytvářet a spravovat skupiny a její nastavení, jako jsou zásady pro pojmenování a vypršení platnosti. Je důležité pochopit, že přiřazení uživatele k této roli dává možnost spravovat všechny skupiny v organizaci napříč různými úlohami, jako jsou týmy, SharePoint, Yammer kromě Outlooku. Uživatel bude také moci spravovat různá nastavení skupin na různých portálech pro správu, jako je například centrum pro správu Microsoft, Azure Portal a také konkrétní úlohy, jako jsou týmy a centra pro správu služby SharePoint.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Groups/assignLicense | Přiřazení licencí k produktu skupinám pro licencování na základě skupin |
> | Microsoft. Directory/Groups/Create | Vytváření skupin s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/DELETE | Odstranit skupiny s výjimkou skupiny přiřazení role |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Čtení skrytých členů skupiny |
> | Microsoft. Directory/Groups/reprocessLicenseAssignment | Opětovné zpracování přiřazení licencí pro licencování na základě skupin |
> | Microsoft. Directory/skupiny/obnovení | Obnovení odstraněných skupin |
> | Microsoft. Directory/Groups/Basic/Update | Aktualizovat základní vlastnosti u skupin s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/klasifikací/Update | Aktualizovat vlastnost klasifikace skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/dynamicMembershipRule/Update | Aktualizovat dynamické pravidlo členství skupin s výjimkou skupin, které lze přiřadit k rolím |
> | Microsoft. Directory/Groups/groupType/Update | Aktualizace vlastnosti groupType pro skupinu |
> | Microsoft. Directory/Groups/Members/Update | Aktualizace členů skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/onPremWriteBack/Update | Aktualizace skupin Azure Active Directory pro zápis do místního prostředí s využitím Azure AD Connect |
> | Microsoft. Directory/Groups/Owners/Update | Aktualizace vlastníků skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/Settings/Update | Aktualizovat nastavení skupin |
> | Microsoft. Directory/Groups/Visibility/Update | Aktualizuje vlastnost viditelnost skupin. |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-All-Application-Permissions | Udělení instančního objektu s přímým přístupem k datům skupiny |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="guest-inviter"></a>Pozvánka hosta

Uživatelé v této roli můžou spravovat Azure Active Directory pozvání uživatele hosta B2B, když **můžou členové pozvat** uživatele na hodnotu ne. Další informace o spolupráci B2B v rámci spolupráce B2B v [Azure AD](../external-identities/what-is-b2b.md). Nezahrnuje žádná další oprávnění.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Users/inviteGuest | Pozvání uživatelů typu host |
> | Microsoft. Directory/uživatelé/standardní/číst | Číst základní vlastnosti uživatelů |
> | Microsoft. Directory/Users/appRoleAssignments/Read | Čtení přiřazení rolí aplikace uživatelům |
> | Microsoft. Directory/Users/directReports/Read | Čtení přímých sestav pro uživatele |
> | Microsoft. Directory/uživatelé/Správce/číst | Správce čtení uživatelů |
> | Microsoft. Directory/uživatelé/memberOf/číst | Číst členství uživatelů ve skupině |
> | Microsoft. Directory/Users/oAuth2PermissionGrants/Read | Číst delegovaná oprávnění pro uživatele |
> | Microsoft. Directory/Users/ownedDevices/Read | Čtení zařízení vlastněných uživateli |
> | Microsoft. Directory/Users/ownedObjects/Read | Číst vlastněné objekty uživatelů |
> | Microsoft. Directory/Users/registeredDevices/Read | Čtení registrovaných zařízení uživatelů |

## <a name="helpdesk-administrator"></a>Správce helpdesku

Uživatelé s touto rolí můžou měnit hesla, zrušit platnost tokenů aktualizace, spravovat žádosti o služby a monitorovat stav služby. Zrušení platnosti aktualizačního tokenu vynutí, aby se uživatel znovu přihlásil. Zda může správce helpdesku resetovat heslo uživatele a zrušit platnost tokenů aktualizace, závisí na roli, kterou uživatel přiřadí. Seznam rolí, u kterých správce helpdesku může resetovat hesla pro a zrušit platnost obnovovacích tokenů, najdete v tématu [oprávnění k resetování hesla](#password-reset-permissions).

> [!IMPORTANT]
> Uživatelé s touto rolí můžou měnit hesla pro lidi, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i mimo Azure Active Directory. Změna hesla uživatele může znamenat možnost předpokládat identitu a oprávnění tohoto uživatele. Například:
>
>- Registrace aplikace a vlastníci podnikových aplikací, kteří můžou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace můžou mít privilegovaná oprávnění ve službě Azure AD a jinde nejsou udělená správcům helpdesku. Prostřednictvím této cesty může správce helpdesku předpokládat identitu vlastníka aplikace a následně převzít identitu privilegované aplikace tím, že aktualizuje přihlašovací údaje pro aplikaci.
>- Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
>- Skupina zabezpečení a Microsoft 365 vlastníci skupiny, kteří mohou spravovat členství ve skupině. Tyto skupiny můžou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure AD a jinde.
>- Správci v jiných službách mimo Azure AD, jako je Exchange Online, Centrum zabezpečení a dodržování předpisů pro Office a systémy lidských zdrojů.
>- Nesprávci jako vedoucí pracovníci, právní poradce a zaměstnanci lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

Delegování oprávnění pro správu pro podmnožiny uživatelů a používání zásad u podmnožiny uživatelů je možné s [jednotkami pro správu](administrative-units.md).

Tato role se dřív nazývala "správce hesel" ve [Azure Portal](https://portal.azure.com/). Název "správce helpdesku" ve službě Azure AD se teď shoduje s názvem v Azure AD PowerShellu a rozhraním Microsoft Graph API.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Číst metadata a klíč nástroje BitLocker na zařízeních |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Vynutit odhlášení pomocí neověřování tokenů aktualizace uživatelů |
> | Microsoft. Directory/uživatelé/heslo/aktualizace | Resetovat hesla pro všechny uživatele |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="hybrid-identity-administrator"></a>Správce hybridní identity

Uživatelé v této roli můžou vytvářet, spravovat a nasazovat nastavení konfigurace zřizování z AD do Azure AD pomocí zřizování cloudu a také spravovat Azure AD Connect a nastavení federace. Uživatelé mohou také řešit a monitorovat protokoly pomocí této role.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/aplikace/vytvořit | Vytvořit všechny typy aplikací |
> | Microsoft. Directory/aplikace/odstranit | Odstranit všechny typy aplikací |
> | Microsoft. Directory/Applications/appRoles/Update | Aktualizuje vlastnost appRoles u všech typů aplikací. |
> | Microsoft. Directory/Applications/publikum/Update | Aktualizace vlastnosti cílové skupiny pro aplikace |
> | Microsoft. Directory/Applications/Authentication/Update | Aktualizovat ověřování u všech typů aplikací |
> | Microsoft. Directory/Applications/Basic/Update | Aktualizace základních vlastností pro aplikace |
> | Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizovat přihlašovací údaje aplikace |
> | Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizovat vlastníky aplikací |
> | Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizovat vystavená oprávnění a požadovaná oprávnění pro všechny typy aplikací |
> | Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizovat zásady aplikací |
> | Microsoft. Directory/aplikace/synchronizace/standardní/číst | Čtení nastavení zřizování přidružených k objektu aplikace |
> | Microsoft. Directory/applicationTemplates/instance | Vytvoření instance aplikací galerie z šablon aplikací |
> | Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti v protokolech auditu, včetně privilegovaných vlastností. |
> | Microsoft. Directory/cloudProvisioning/allProperties/allTasks | Přečtěte si a nakonfigurujte všechny vlastnosti služby Azure AD Cloud Provisioning. |
> | Microsoft. Directory/domén/allProperties/Read | Načte všechny vlastnosti domén. |
> | Microsoft. Directory/domény/federace/aktualizace | Aktualizovat federační vlastnost domén |
> | Microsoft. Directory/Organization/dirSync/Update | Aktualizovat vlastnost synchronizace adresáře organizace |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování. |
> | Microsoft. Directory/servicePrincipals/Create | Vytvoření instančních objektů |
> | Microsoft. Directory/servicePrincipals/DELETE | Odstranění objektů služby |
> | Microsoft. Directory/servicePrincipals/Disable | Zakázat instanční objekty |
> | Microsoft. Directory/servicePrincipals/Enable | Povolení instančních objektů |
> | Microsoft. Directory/servicePrincipals/synchronizationCredentials/Manage | Správa tajných klíčů a přihlašovacích údajů pro zřizování aplikací |
> | Microsoft. Directory/servicePrincipals/synchronizationJobs/Manage | Spusťte, restartujte a pozastavte úlohy synchronizace – zřizování aplikací. |
> | Microsoft. Directory/servicePrincipals/synchronizationSchema/Manage | Vytváření a Správa synchronizace – úloh a schémat pro zřizování aplikací |
> | Microsoft. Directory/servicePrincipals/publikum/Update | Aktualizovat vlastnosti cílové skupiny u instančních objektů |
> | Microsoft. Directory/servicePrincipals/Authentication/Update | Aktualizovat vlastnosti ověřování u instančních objektů |
> | Microsoft. Directory/servicePrincipals/Basic/Update | Aktualizace základních vlastností u instančních objektů |
> | Microsoft. Directory/servicePrincipals/přihlašovací údaje/aktualizace | Aktualizovat přihlašovací údaje objektů služby |
> | Microsoft. Directory/servicePrincipals/Owners/Update | Aktualizujte vlastníky instančních objektů. |
> | Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizovat oprávnění instančních objektů |
> | Microsoft. Directory/servicePrincipals/policies/Update | Aktualizovat zásady instančních objektů |
> | Microsoft. Directory/servicePrincipals/tag/Update | Aktualizace vlastnosti značky pro instanční objekty |
> | Microsoft. Directory/servicePrincipals/Synchronization/Standard/Read | Čtení nastavení zřizování přidružených k vašemu instančnímu objektu |
> | Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti v sestavách přihlášení, včetně privilegovaných vlastností. |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. messageCenter/Messages/Read | Čtení zpráv v centru zpráv v centru pro správu Microsoft 365 s výjimkou zpráv zabezpečení |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="insights-administrator"></a>Správce Insights

Uživatelé v této roli mají přístup k plné sadě možností správy v [aplikaci M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Tato role má možnost číst informace o adresáři, monitorovat stav služby, lístky podpory souborů a přistupovat k aspektům nastavení pro správu Insights.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Insights/allEntities/allTasks | Správa všech aspektů aplikace Insights |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="insights-business-leader"></a>Vedoucí firmy pro Insights

Uživatelé v této roli mají přístup k sadě řídicích panelů a přehledů prostřednictvím [aplikace M365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). To zahrnuje plný přístup ke všem řídicím panelům a prezentovaným přehledům a funkcím průzkumu dat. Uživatelé v této roli nemají přístup k nastavení konfigurace produktu, což je zodpovědnost role správce Insights.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Insights/sestavy/číst | Zobrazení sestav a řídicího panelu v aplikaci Insights |
> | Microsoft. Insights/programy/aktualizace | Nasazení a Správa programů v aplikaci Insights |

## <a name="intune-administrator"></a>Správce Intune

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Intune online, pokud je tato služba k dispozici. Kromě toho tato role obsahuje možnost spravovat uživatele a zařízení, aby bylo možné přidružit zásady, a také vytvářet a spravovat skupiny. Další informace najdete v [rámci řízení správy na základě rolí (RBAC) pomocí Microsoft Intune](/intune/role-based-access-control).

Tato role může vytvářet a spravovat všechny skupiny zabezpečení. Správce Intune ale nemá oprávnění správce přes skupiny Office. To znamená, že správce nemůže aktualizovat vlastníky ani členství všech skupin Office v organizaci. Může ale spravovat skupinu Office, kterou vytvoří, která je součástí svých oprávnění koncových uživatelů. Všechny skupiny Office (nikoli skupina zabezpečení), které vytvoří, by se měly počítat s kvótou 250.

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako "Správce služby Intune". Ve [Azure Portal](https://portal.azure.com)je to správce Intune.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Číst metadata a klíč nástroje BitLocker na zařízeních |
> | Microsoft. Directory/Contacts/Create | Vytvoření kontaktů |
> | Microsoft. Directory/Contacts/DELETE | Odstranit kontakty |
> | Microsoft. Directory/Contacts/Basic/Update | Aktualizace základních vlastností u kontaktů |
> | Microsoft. Directory/Devices/Create | Vytváření zařízení (registrace ve službě Azure AD) |
> | Microsoft. Directory/Devices/DELETE | Odstranění zařízení ze služby Azure AD |
> | Microsoft. Directory/Devices/Disable | Zakázat zařízení ve službě Azure AD |
> | Microsoft. Directory/Devices/Enable | Povolení zařízení ve službě Azure AD |
> | Microsoft. Directory/Devices/Basic/Update | Aktualizace základních vlastností na zařízeních |
> | Microsoft. Directory/Devices/extensionAttributes/Update | Aktualizuje všechny hodnoty pro vlastnost Devices. extensionAttributes. |
> | Microsoft. Directory/Devices/registeredOwners/Update | Aktualizace registrovaných vlastníků zařízení |
> | Microsoft. Directory/Devices/registeredUsers/Update | Aktualizace registrovaných uživatelů zařízení |
> | Microsoft. Directory/deviceManagementPolicies/Standard/Read | Číst standardní vlastnosti v zásadách aplikací pro správu zařízení |
> | Microsoft. Directory/deviceRegistrationPolicy/Standard/Read | Číst standardní vlastnosti v zásadách registrace zařízení |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Čtení skrytých členů skupiny |
> | Microsoft. Directory/groups. Security/Create | Vytvoření skupin zabezpečení s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/groups. Security/DELETE | Odstranit skupiny zabezpečení s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/groups. Security/Basic/Update | Aktualizace základních vlastností u skupin zabezpečení s vyloučením skupin, které je možné přiřadit k rolím |
> | Microsoft. Directory/groups. Security/Classification/Update | Aktualizovat vlastnost klasifikace skupin zabezpečení s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/groups. Security/dynamicMembershipRule/Update | Aktualizuje vlastnost dynamicMembershipRule skupin zabezpečení s vyloučením skupin, které se přiřazují role. |
> | Microsoft. Directory/groups. Security/Members/Update | Aktualizace členů skupin zabezpečení s vyloučením skupin pro přiřazení rolí |
> | Microsoft. Directory/groups. Security/Owners/Update | Aktualizace vlastníků skupin zabezpečení s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/groups. Security/Visibility/Update | Aktualizovat vlastnost Visibility skupin zabezpečení s vyloučením skupin, které je možné přiřadit k rolím |
> | Microsoft. Directory/uživatelé/Basic/Update | Aktualizace základních vlastností u uživatelů |
> | Microsoft. Directory/uživatelé/správce/aktualizace | Správce aktualizací pro uživatele |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Intune/allEntities/allTasks | Umožňuje spravovat všechny aspekty Microsoft Intune. |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="kaizala-administrator"></a>Správce Kaizala

Uživatelé s touto rolí mají globální oprávnění ke správě nastavení v rámci Microsoft Kaizala, kdy je služba k dispozici, a také možnost spravovat lístky podpory a monitorovat stav služby. Kromě toho může uživatel získat přístup k sestavám souvisejícím s přijetím & používání Kaizala podle členů organizace a obchodních sestav vytvořených pomocí akcí Kaizala.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="knowledge-administrator"></a>Správce znalostní báze

Uživatelé v této roli mají plný přístup ke všem nastavením znalostní báze, učení a inteligentních funkcí v centru pro správu Microsoft 365. Mají všeobecně pochopit sadu produktů, licenční údaje a jejich zodpovědnost za řízení přístupu. Správce znalostní báze může vytvářet a spravovat obsah, jako jsou témata, akronymy a studijní materiály. Tito uživatelé navíc mohou vytvářet centra obsahu, monitorovat stav služeb a vytvářet žádosti o služby.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/groups. Security/Create | Vytvoření skupin zabezpečení s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/groups. Security/createAsOwner | Vytvoření skupin zabezpečení s vyloučením skupin, přiřazení rolí a autora se přidá jako první vlastník. |
> | Microsoft. Directory/groups. Security/DELETE | Odstranit skupiny zabezpečení s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/groups. Security/Basic/Update | Aktualizace základních vlastností u skupin zabezpečení s vyloučením skupin, které je možné přiřadit k rolím |
> | Microsoft. Directory/groups. Security/Members/Update | Aktualizace členů skupin zabezpečení s vyloučením skupin pro přiřazení rolí |
> | Microsoft. Directory/groups. Security/Owners/Update | Aktualizace vlastníků skupin zabezpečení s vyloučením skupin s přiřazením rolí |
> | Microsoft. Office 365. Knowledge/contentUnderstanding/allProperties/allTasks | Čtení a aktualizace všech vlastností porozumění obsahu v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. Knowledge/knowledgeNetwork/allProperties/allTasks | Čtení a aktualizace všech vlastností znalostní sítě v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. protectionCenter/sensitivityLabels/allProperties/Read | Načte všechny vlastnosti popisků citlivosti v centrech zabezpečení a dodržování předpisů. |
> | Microsoft. Office 365. sharePoint/allEntities/allTasks | Vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v SharePointu |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="license-administrator"></a>Správce licencí

Uživatelé v této roli můžou přidávat, odebírat a aktualizovat přiřazení licencí pro uživatele, skupiny (pomocí licencování na základě skupin) a spravovat umístění používání u uživatelů. Role neuděluje možnost kupovat ani spravovat odběry, vytvářet a spravovat skupiny ani vytvářet ani spravovat uživatele nad rámec tohoto místa použití. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Groups/assignLicense | Přiřazení licencí k produktu skupinám pro licencování na základě skupin |
> | Microsoft. Directory/Groups/reprocessLicenseAssignment | Opětovné zpracování přiřazení licencí pro licencování na základě skupin |
> | Microsoft. Directory/Users/assignLicense | Správa uživatelských licencí |
> | Microsoft. Directory/Users/reprocessLicenseAssignment | Znovu zpracovat přiřazení licencí pro uživatele |
> | Microsoft. Directory/Users/usageLocation/Update | Aktualizovat umístění uživatelů při použití |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="message-center-privacy-reader"></a>Čtenář ochrany osobních údajů centra zpráv

Uživatelé v této roli můžou monitorovat všechna oznámení v centru zpráv, včetně zpráv o soukromí dat. Čtenáři ochrany osobních údajů centra zpráv získají e-mailová oznámení, včetně těch, které se týkají ochrany osobních údajů a můžou zrušit odběr pomocí předvoleb centra zpráv. Jenom globální správce a čtenář ochrany osobních údajů centra zpráv můžou číst zprávy o ochraně dat. Tato role navíc obsahuje možnost zobrazení skupin, domén a předplatných. Tato role nemá oprávnění k zobrazení, vytvoření nebo správě žádostí o služby.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. messageCenter/Messages/Read | Čtení zpráv v centru zpráv v centru pro správu Microsoft 365 s výjimkou zpráv zabezpečení |
> | Microsoft. Office 365. messageCenter/securityMessages/Read | Čtení zpráv zabezpečení v centru zpráv v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="message-center-reader"></a>Čtenář centra zpráv

Uživatelé v této roli můžou monitorovat oznámení a poradenské aktualizace stavu v [centru zpráv](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pro jejich organizaci na konfigurovaných službách, jako je Exchange, Intune a Microsoft Teams. Čtečky centra zpráv obdrží týdenní e-mailové zprávy o příspěvcích, aktualizacích a můžou sdílet příspěvky centra zpráv v Microsoft 365. Uživatelé přiřazení k této roli ve službě Azure AD budou mít přístup jen pro čtení ke službám Azure AD, jako jsou uživatelé a skupiny. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. messageCenter/Messages/Read | Čtení zpráv v centru zpráv v centru pro správu Microsoft 365 s výjimkou zpráv zabezpečení |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="modern-commerce-user"></a>Moderní uživatel pro obchodování

Nepoužívat. Tato role se automaticky přiřadí z obchodu a není určená ani podporovaná pro jiné použití. Níže najdete podrobnosti.

Role uživatele moderního obchodování poskytuje určitým uživatelům oprávnění k přístupu k centru pro správu Microsoft 365 a zobrazení levého navigačního panelu pro **domovskou stránku**, **fakturaci** a **podporu**. Obsah, který je dostupný v těchto oblastech, se řídí [rolemi specifickými pro obchod](../../cost-management-billing/manage/understand-mca-roles.md) , které jsou přiřazené uživatelům pro správu produktů, které si koupili pro sebe nebo vaši organizaci. To může zahrnovat úkoly, jako jsou platby za účty, nebo přístup k fakturačním účtům a profilům fakturace.

Uživatelé s uživatelskou rolí moderního obchodování mají obvykle oprávnění správce v dalších nákupních systémech Microsoftu, ale nemají role globálního správce nebo správce fakturace používané pro přístup do centra pro správu.

**Kdy je role uživatele moderního obchodování přiřazená?**

* **Nákup samoobslužných služeb v centru pro správu Microsoft 365** – nákup samoobslužných služeb dává uživatelům možnost vyzkoušet si nové produkty, a to tak, že si je zakoupí nebo zaregistrují sami. Tyto produkty se spravují v centru pro správu. Uživatelům, kteří si zakoupí samoobslužné služby, se přiřadí role v systému pro obchodování a moderní obchodní role, aby mohli spravovat své nákupy v centru pro správu. Správci můžou blokovat nákupy samoobslužných služeb (pro Power BI, Power Apps, Power Automate) prostřednictvím [PowerShellu](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell). Další informace najdete v [nejčastějších dotazech k samoobslužnému nákupu](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).
* **Nákupy od komerčního tržiště Microsoftu** – podobně jako při nákupu samoobslužných služeb, když uživatel koupí produkt nebo službu z Microsoft AppSource nebo Azure Marketplace, přiřadí se moderní obchodní role uživatele, pokud nemají roli globálního správce nebo správce fakturace. V některých případech je možné, že uživatelé můžou tyto nákupy zablokovat. Další informace najdete v tématu [komerční tržiště Microsoftu](../../marketplace/marketplace-faq-publisher-guide.md#what-could-block-a-customer-from-completing-a-purchase).
* **Návrhy od Microsoftu** – návrh je formální nabídka Microsoftu, která vaší organizaci koupí produkty a služby Microsoftu. Když osoba, která přijímá návrh, nemá roli globálního správce nebo správce fakturace ve službě Azure AD, přiřadí se mu role specifická pro obchod pro dokončení návrhu a role uživatele moderního obchodování pro přístup k centru pro správu. Když přistupují k centru pro správu, můžou používat jenom funkce, které jsou autorizované v rámci své obchodní role.
* **Role specifické pro obchod** – někteří uživatelé mají přiřazeny role specifické pro obchod. Pokud uživatel není globálním správcem nebo správcem fakturace, získá uživatel moderní obchodní role, aby mohl získat přístup k centru pro správu.

Pokud je role uživatele moderního obchodování Nepřiřazená uživateli, ztratí přístup k centru pro správu Microsoft 365. Pokud by nějaké produkty spravovaly buď pro sebe, nebo pro vaši organizaci, nebudou je moct spravovat. To může zahrnovat přiřazení licencí, změnu způsobů platby, placení účtů nebo jiné úlohy pro správu předplatných.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. obchod. fakturace/partneři/čtení | Číst vlastnost partner Microsoft 365 fakturace |
> | Microsoft. Commerce. volumeLicenseServiceCenter/allEntities/allTasks | Správa všech aspektů služby Volume Licensing Service Center |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Basic/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="network-administrator"></a>Správce sítě

Uživatelé v této roli můžou zkontrolovat doporučení k architektuře hraničního sítě od Microsoftu, která jsou založená na telemetrie sítě od jejich uživatelských umístění. Výkon sítě pro Microsoft 365 spoléhá na pečlivou architekturu hraniční sítě zákazníka v podniku, která je obecně specifická pro konkrétní uživatelské umístění. Tato role umožňuje upravovat zjištěná umístění uživatelů a konfiguraci síťových parametrů pro tato umístění, aby se usnadnila Vylepšená měření telemetrie a doporučení pro návrh.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. Network/Locations/allProperties/allTasks | Správa všech aspektů síťových umístění |
> | Microsoft. Office 365. Network/Performance/allProperties/Read | Čtení všech vlastností výkonu sítě v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="office-apps-administrator"></a>Správce aplikací Office

Uživatelé v této roli můžou spravovat nastavení cloudu pro Microsoft 365 aplikace. To zahrnuje správu zásad cloudu, samoobslužné správy stahování a možnosti Zobrazit sestavu související s aplikacemi Office. Tato role navíc uděluje možnost spravovat lístky podpory a monitorovat stav služby v hlavním centru pro správu. Uživatelé přiřazení k této roli můžou také spravovat komunikaci s novými funkcemi v aplikacích Office.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. messageCenter/Messages/Read | Čtení zpráv v centru zpráv v centru pro správu Microsoft 365 s výjimkou zpráv zabezpečení |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. userCommunication/allEntities/allTasks | Přečtěte si a aktualizujte viditelnost novinek zpráv |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="partner-tier1-support"></a>Podpora partnerů Tier1

Nepoužívat. Tato role se už nepoužívá a v budoucnu se odebere z Azure AD. Tato role je určená pro použití malým počtem partnerů Microsoftu pro prodej a není určená pro obecné použití.

> [!IMPORTANT]
> Tato role může resetovat hesla a zrušit platnost tokenů aktualizace jenom pro správce bez oprávnění správce. Tato role by se neměla používat, protože je zastaralá a už se nebude vracet v rozhraní API.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Applications/appRoles/Update | Aktualizuje vlastnost appRoles u všech typů aplikací. |
> | Microsoft. Directory/Applications/publikum/Update | Aktualizace vlastnosti cílové skupiny pro aplikace |
> | Microsoft. Directory/Applications/Authentication/Update | Aktualizovat ověřování u všech typů aplikací |
> | Microsoft. Directory/Applications/Basic/Update | Aktualizace základních vlastností pro aplikace |
> | Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizovat přihlašovací údaje aplikace |
> | Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizovat vlastníky aplikací |
> | Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizovat vystavená oprávnění a požadovaná oprávnění pro všechny typy aplikací |
> | Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizovat zásady aplikací |
> | Microsoft. Directory/Contacts/Create | Vytvoření kontaktů |
> | Microsoft. Directory/Contacts/DELETE | Odstranit kontakty |
> | Microsoft. Directory/Contacts/Basic/Update | Aktualizace základních vlastností u kontaktů |
> | Microsoft. Directory/Groups/Create | Vytváření skupin s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/DELETE | Odstranit skupiny s výjimkou skupiny přiřazení role |
> | Microsoft. Directory/skupiny/obnovení | Obnovení odstraněných skupin |
> | Microsoft. Directory/Groups/Members/Update | Aktualizace členů skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/Owners/Update | Aktualizace vlastníků skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Umožňuje vytvářet a odstraňovat oprávnění OAuth 2,0 a číst a aktualizovat všechny vlastnosti. |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizovat přiřazení rolí instančního objektu |
> | Microsoft. Directory/Users/assignLicense | Správa uživatelských licencí |
> | Microsoft. Directory/uživatelé/vytvořit | Přidání uživatelů |
> | Microsoft. Directory/Users/DELETE | Odstranění uživatelů |
> | Microsoft. Directory/Users/Disable | Zakázat uživatele |
> | Microsoft. Directory/uživatelé/povolit | Povolit uživatele |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Vynutit odhlášení pomocí neověřování tokenů aktualizace uživatelů |
> | Microsoft. Directory/Users/Restore | Obnovit odstraněné uživatele |
> | Microsoft. Directory/uživatelé/Basic/Update | Aktualizace základních vlastností u uživatelů |
> | Microsoft. Directory/uživatelé/správce/aktualizace | Správce aktualizací pro uživatele |
> | Microsoft. Directory/uživatelé/heslo/aktualizace | Resetovat hesla pro všechny uživatele |
> | Microsoft. Directory/uživatelé/userPrincipalName/Update | Aktualizovat hlavní název uživatele uživatelů |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="partner-tier2-support"></a>Podpora partnerů 2

Nepoužívat. Tato role se už nepoužívá a v budoucnu se odebere z Azure AD. Tato role je určená pro použití malým počtem partnerů Microsoftu pro prodej a není určená pro obecné použití.

> [!IMPORTANT]
> Tato role může resetovat hesla a zrušit platnost obnovovacích tokenů pro všechny správce a správce, kteří nejsou správci (včetně globálních správců). Tato role by se neměla používat, protože je zastaralá a už se nebude vracet v rozhraní API.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Applications/appRoles/Update | Aktualizuje vlastnost appRoles u všech typů aplikací. |
> | Microsoft. Directory/Applications/publikum/Update | Aktualizace vlastnosti cílové skupiny pro aplikace |
> | Microsoft. Directory/Applications/Authentication/Update | Aktualizovat ověřování u všech typů aplikací |
> | Microsoft. Directory/Applications/Basic/Update | Aktualizace základních vlastností pro aplikace |
> | Microsoft. Directory/aplikace/přihlašovací údaje/aktualizace | Aktualizovat přihlašovací údaje aplikace |
> | Microsoft. Directory/aplikace/vlastníci/aktualizace | Aktualizovat vlastníky aplikací |
> | Microsoft. Directory/aplikace/oprávnění/aktualizace | Aktualizovat vystavená oprávnění a požadovaná oprávnění pro všechny typy aplikací |
> | Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizovat zásady aplikací |
> | Microsoft. Directory/Contacts/Create | Vytvoření kontaktů |
> | Microsoft. Directory/Contacts/DELETE | Odstranit kontakty |
> | Microsoft. Directory/Contacts/Basic/Update | Aktualizace základních vlastností u kontaktů |
> | Microsoft. Directory/domény/allProperties/allTasks | Vytváření a odstraňování domén a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/Groups/Create | Vytváření skupin s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/DELETE | Odstranit skupiny s výjimkou skupiny přiřazení role |
> | Microsoft. Directory/skupiny/obnovení | Obnovení odstraněných skupin |
> | Microsoft. Directory/Groups/Members/Update | Aktualizace členů skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/Owners/Update | Aktualizace vlastníků skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Umožňuje vytvářet a odstraňovat oprávnění OAuth 2,0 a číst a aktualizovat všechny vlastnosti. |
> | Microsoft. Directory/Organization/Basic/Update | Aktualizace základních vlastností v organizaci |
> | Microsoft. Directory/roleAssignments/allProperties/allTasks | Vytváření a odstraňování přiřazení rolí a čtení a aktualizace vlastností přiřazení rolí |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Vytváření a odstraňování definic rolí a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | Vytváření a odstraňování scopedRoleMemberships a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizovat přiřazení rolí instančního objektu |
> | Microsoft. Directory/subscribedSkus/Standard/Read | Číst základní vlastnosti v předplatných |
> | Microsoft. Directory/Users/assignLicense | Správa uživatelských licencí |
> | Microsoft. Directory/uživatelé/vytvořit | Přidání uživatelů |
> | Microsoft. Directory/Users/DELETE | Odstranění uživatelů |
> | Microsoft. Directory/Users/Disable | Zakázat uživatele |
> | Microsoft. Directory/uživatelé/povolit | Povolit uživatele |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Vynutit odhlášení pomocí neověřování tokenů aktualizace uživatelů |
> | Microsoft. Directory/Users/Restore | Obnovit odstraněné uživatele |
> | Microsoft. Directory/uživatelé/Basic/Update | Aktualizace základních vlastností u uživatelů |
> | Microsoft. Directory/uživatelé/správce/aktualizace | Správce aktualizací pro uživatele |
> | Microsoft. Directory/uživatelé/heslo/aktualizace | Resetovat hesla pro všechny uživatele |
> | Microsoft. Directory/uživatelé/userPrincipalName/Update | Aktualizovat hlavní název uživatele uživatelů |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="password-administrator"></a>Správce hesel

Uživatelé s touto rolí mají omezené možnosti správy hesel. Tato role neuděluje schopnost spravovat žádosti o služby nebo monitorovat stav služby. Zda může správce hesla resetovat heslo uživatele, závisí na roli, kterou uživatel přiřadí. Seznam rolí, pro které může správce hesel resetovat hesla pro, najdete v tématu [oprávnění k resetování hesla](#password-reset-permissions).

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/uživatelé/heslo/aktualizace | Resetovat hesla pro všechny uživatele |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="power-bi-administrator"></a>Správce Power BI

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Power BI, když je služba přítomná, a také možnost spravovat lístky podpory a monitorovat stav služby. Další informace o [principu role správce Power BI](/power-bi/service-admin-role).

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako "Power BI Správce služby". V [Azure Portal](https://portal.azure.com)je "Power BI správce".

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |
> | Microsoft. powerApps. powerBI/allEntities/allTasks | Umožňuje spravovat všechny aspekty Power BI. |

## <a name="power-platform-administrator"></a>Správce Power Platform

Uživatelé v této roli můžou vytvářet a spravovat všechny aspekty prostředí, PowerApps, toků a zásady ochrany před únikem informací. Kromě toho uživatelé s touto rolí mají možnost spravovat lístky podpory a monitorovat stav služby.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. dynamics365/allEntities/allTasks | Správa všech aspektů Dynamics 365 |
> | Microsoft. Flow/allEntities/allTasks | Správa všech aspektů Microsoft Power automatizuje |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |
> | Microsoft. powerApps/allEntities/allTasks | Správa všech aspektů Power Apps |

## <a name="printer-administrator"></a>Správce tiskárny

Uživatelé v této roli můžou registrovat tiskárny a spravovat všechny aspekty všech konfigurací tiskáren v rámci univerzálního tiskového řešení Microsoftu, včetně nastavení univerzálního tiskového konektoru. Můžou si udělit souhlas s všemi delegovanými žádostmi o oprávnění k tisku. Správci tiskáren mají také přístup k tiskovým sestavám.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. Print/allEntities/allProperties/allTasks | Vytváření a odstraňování tiskáren a konektorů a čtení a aktualizace všech vlastností v Microsoft Print |

## <a name="printer-technician"></a>Technik tiskárny

Uživatelé s touto rolí můžou registrovat tiskárny a spravovat stav tiskáren v rámci univerzálního tiskového řešení Microsoftu. Můžou si taky přečíst všechny informace o konektoru. Klíčová úloha: technik tiskárny nemůže provést nastavení uživatelských oprávnění na tiskárnách a sdílení tiskáren.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. Print/Connectors/allProperties/Read | Číst všechny vlastnosti konektorů v Microsoft tisku |
> | Microsoft. Azure. Printing/Printers/allProperties/Read | Čtení všech vlastností tiskáren v Microsoft Print |
> | Microsoft. Azure. tisk/tiskárny/registrovat | Registrace tiskáren v Microsoft tisku |
> | Microsoft. Azure. tisk/tiskárny/zrušit registraci | Zrušení registrace tiskáren v Microsoft tisku |
> | Microsoft. Azure. tisk/tiskárny/Basic/Update | Aktualizace základních vlastností tiskáren v Microsoft Print |

## <a name="privileged-authentication-administrator"></a>Správce privilegovaného ověřování

Uživatelé s touto rolí můžou nastavit nebo resetovat jakoukoli metodu ověřování (včetně hesel) pro libovolného uživatele, včetně globálních správců. Správci privilegovaného ověřování můžou vynutit, aby uživatelé znovu zaregistrovali u stávajících přihlašovacích údajů bez hesla (například MFA nebo FIDO) a odvolali si MFA na zařízení. při příštím přihlášení všech uživatelů se zobrazí dotaz na MFA.

Role [Správce ověřování](#authentication-administrator) má oprávnění k vynucení opětovné registrace a ověřování službou Multi-Factor Authentication pro standardní uživatele a uživatele s některými rolemi správce.

Role [správce zásad ověřování](#authentication-policy-administrator) má oprávnění k nastavení zásad ověřování klienta, které určuje, které metody může každý uživatel registrovat a používat.

| Role | Umožňuje spravovat metody ověřování uživatele. | Správa MFA pro jednotlivé uživatele | Správa nastavení vícefaktorového ověřování | Správa zásad metod ověřování | Spravovat zásady ochrany heslem |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Správce ověřování | Ano pro některé uživatele (viz výše) | Ano pro některé uživatele (viz výše) | No | No | No |
| Správce privilegovaného ověřování| Ano pro všechny uživatele | Ano pro všechny uživatele | No | No | No |
| Správce zásad ověřování | No | No | Yes | Yes | Yes |

> [!IMPORTANT]
> Uživatelé s touto rolí můžou měnit přihlašovací údaje pro uživatele, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i mimo Azure Active Directory. Změna přihlašovacích údajů uživatele může znamenat možnost předpokládat identitu a oprávnění tohoto uživatele. Například:
>
>* Registrace aplikace a vlastníci podnikových aplikací, kteří můžou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace můžou mít privilegovaná oprávnění ve službě Azure AD a jinde nejsou udělená správcům ověřování. Prostřednictvím této cesty může správce ověřování předpokládat identitu vlastníka aplikace a následně převzít identitu privilegované aplikace tím, že aktualizuje přihlašovací údaje pro aplikaci.
>* Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
>* Skupina zabezpečení a Microsoft 365 vlastníci skupiny, kteří mohou spravovat členství ve skupině. Tyto skupiny můžou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure AD a jinde.
>* Správci v jiných službách mimo Azure AD, jako je Exchange Online, Centrum zabezpečení a dodržování předpisů pro Office a systémy lidských zdrojů.
>* Nesprávci jako vedoucí pracovníci, právní poradce a zaměstnanci lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.


> [!IMPORTANT]
> Tato role v tuto chvíli není schopná spravovat MFA pro uživatele na portálu pro správu starší verze MFA. Stejné funkce můžete provést pomocí modulu [set-MsolUser](/powershell/module/msonline/set-msoluser) RUTINY Azure AD PowerShell.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Vynutit odhlášení pomocí neověřování tokenů aktualizace uživatelů |
> | Microsoft. Directory/Users/strongAuthentication/Update | Aktualizace vlastnosti silného ověřování pro uživatele |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="privileged-role-administrator"></a>Správce privilegovaných rolí

Uživatelé s touto rolí můžou spravovat přiřazení rolí v Azure Active Directory a taky v Azure AD Privileged Identity Management. Můžou vytvářet a spravovat skupiny, které se dají přiřadit k rolím Azure AD. Kromě toho tato role umožňuje správu všech aspektů Privileged Identity Management a jednotek pro správu.

> [!IMPORTANT]
> Tato role uděluje možnost spravovat přiřazení pro všechny role Azure AD, včetně role globálního správce. Tato role nezahrnuje žádné další privilegované možnosti ve službě Azure AD, jako je vytváření nebo aktualizace uživatelů. Uživatelé přiřazení k této roli ale můžou udělit další role nebo jiné další oprávnění.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/administrativeUnits/allProperties/allTasks | Vytváření a Správa jednotek pro správu (včetně členů) |
> | Microsoft. Directory/appRoleAssignments/allProperties/allTasks | Vytváření a odstraňování appRoleAssignments a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/authorizationPolicy/allProperties/allTasks | Správa všech aspektů zásad autorizace |
> | Microsoft. Directory/directoryRoles/allProperties/allTasks | Vytváření a odstraňování rolí adresáře a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/groupsAssignableToRoles/Create | Vytváření skupin s možností přiřazení rolí |
> | Microsoft. Directory/groupsAssignableToRoles/DELETE | Odstranit roli – přiřaditelné skupiny |
> | Microsoft. Directory/groupsAssignableToRoles/Restore | Obnovit roli – přiřaditelné skupiny |
> | Microsoft. Directory/groupsAssignableToRoles/allProperties/Update | Aktualizace role – přiřaditelné skupiny |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Umožňuje vytvářet a odstraňovat oprávnění OAuth 2,0 a číst a aktualizovat všechny vlastnosti. |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Privileged Identity Management |
> | Microsoft. Directory/roleAssignments/allProperties/allTasks | Vytváření a odstraňování přiřazení rolí a čtení a aktualizace vlastností přiřazení rolí |
> | Microsoft. Directory/roleDefinitions/allProperties/allTasks | Vytváření a odstraňování definic rolí a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/scopedRoleMemberships/allProperties/allTasks | Vytváření a odstraňování scopedRoleMemberships a čtení a aktualizace všech vlastností |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizovat přiřazení rolí instančního objektu |
> | Microsoft. Directory/servicePrincipals/oprávnění/aktualizace | Aktualizovat oprávnění instančních objektů |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForAll. Microsoft-Company-Admin | Udělení souhlasu pro všechna oprávnění k jakékoli aplikaci |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="reports-reader"></a>Čtečka sestav

Uživatelé s touto rolí mohou zobrazovat data vytváření sestav o využití a řídicí panel sestavy v centru pro správu Microsoft 365 a kontext pro přijetí v Power BI. Role navíc poskytuje přístup k sestavám přihlašování a aktivitám v Azure AD a datům vráceným rozhraním API pro vytváření sestav Microsoft Graph. Uživatel přiřazený k roli čtenáře sestav má přístup pouze k relevantním metrikám využití a přijetí. Nemají žádná oprávnění správce ke konfiguraci nastavení nebo přístupu k centrům pro správu specifickým pro konkrétní produkt, jako je Exchange. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti v protokolech auditu, včetně privilegovaných vlastností. |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování. |
> | Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti v sestavách přihlášení, včetně privilegovaných vlastností. |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Office 365. Network/Performance/allProperties/Read | Čtení všech vlastností výkonu sítě v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Čtení sestav využití Office 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="search-administrator"></a>Správce hledání

Uživatelé v této roli mají plný přístup ke všem funkcím pro správu služby Microsoft Search v centru pro správu Microsoft 365. Kromě toho mohou uživatelé zobrazit Centrum zpráv, monitorovat stav služeb a vytvářet žádosti o služby.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. messageCenter/Messages/Read | Čtení zpráv v centru zpráv v centru pro správu Microsoft 365 s výjimkou zpráv zabezpečení |
> | Microsoft. Office 365. Search/Content/Manage | Vytváření a odstraňování obsahu a čtení a aktualizace všech vlastností ve společnosti Microsoft Search |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="search-editor"></a>Editor hledání

Uživatelé v této roli můžou vytvářet, spravovat a odstraňovat obsah pro Microsoft Search v centru pro správu Microsoft 365, včetně záložek, Q&jako a umístění.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. messageCenter/Messages/Read | Čtení zpráv v centru zpráv v centru pro správu Microsoft 365 s výjimkou zpráv zabezpečení |
> | Microsoft. Office 365. Search/Content/Manage | Vytváření a odstraňování obsahu a čtení a aktualizace všech vlastností ve společnosti Microsoft Search |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="security-administrator"></a>Správce zabezpečení

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

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/aplikace/zásady/aktualizace | Aktualizovat zásady aplikací |
> | Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti v protokolech auditu, včetně privilegovaných vlastností. |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Číst metadata a klíč nástroje BitLocker na zařízeních |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Čtení všech vlastností v Azure AD – Správa nároků |
> | Microsoft. Directory/identityProtection/allProperties/Read | Čtení všech prostředků v Azure AD Identity Protection |
> | Microsoft. Directory/identityProtection/allProperties/Update | Aktualizace všech prostředků v Azure AD Identity Protection |
> | Microsoft. Directory/policies/Create | Vytvoření zásad ve službě Azure AD |
> | Microsoft. Directory/policies/DELETE | Odstranění zásad ve službě Azure AD |
> | Microsoft. Directory/policies/Basic/Update | Aktualizace základních vlastností v zásadách |
> | Microsoft. Directory/policies/Owners/Update | Aktualizovat vlastníky zásad |
> | Microsoft. Directory/policies/tenantDefault/Update | Aktualizace výchozích zásad organizace |
> | Microsoft. Directory/conditionalAccessPolicies/Create | Vytvoření zásad podmíněného přístupu |
> | Microsoft. Directory/conditionalAccessPolicies/DELETE | Odstranění zásad podmíněného přístupu |
> | Microsoft. Directory/conditionalAccessPolicies/Standard/Read | Číst podmíněný přístup pro zásady |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Read | Přečtěte si vlastníky zásad podmíněného přístupu. |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Přečtěte si vlastnost "použito na" pro zásady podmíněného přístupu. |
> | Microsoft. Directory/conditionalAccessPolicies/Basic/Update | Aktualizace základních vlastností pro zásady podmíněného přístupu |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Update | Aktualizace vlastníků pro zásady podmíněného přístupu |
> | Microsoft. Directory/conditionalAccessPolicies/tenantDefault/Update | Aktualizace výchozího tenanta pro zásady podmíněného přístupu |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/Read | Čtení všech prostředků v Privileged Identity Management |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování. |
> | Microsoft. Directory/servicePrincipals/policies/Update | Aktualizovat zásady instančních objektů |
> | Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti v sestavách přihlášení, včetně privilegovaných vlastností. |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. protectionCenter/allEntities/Standard/Read | Načte standardní vlastnosti všech prostředků v centrech zabezpečení a dodržování předpisů. |
> | Microsoft. Office 365. protectionCenter/allEntities/Basic/Update | Aktualizuje základní vlastnosti všech prostředků v centrech zabezpečení a dodržování předpisů. |
> | Microsoft. Office 365. protectionCenter/attackSimulator/datové části/allProperties/allTasks | Vytváření a Správa datových částí útoku v simulátoru útoků |
> | Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/Read | Přečtěte si sestavy pro simulace útoků útoku a související školení. |
> | Microsoft. Office 365. protectionCenter/attackSimulator/simulace/allProperties/allTasks | Vytváření a Správa šablon simulace útoků v simulátoru útoku |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="security-operator"></a>Operátor zabezpečení

Uživatelé s touto rolí můžou spravovat výstrahy a mít globální přístup jen pro čtení k funkcím spojeným se zabezpečením, včetně všech informací v Microsoft 365 Security Center, Azure Active Directory, Identity Protection, Privileged Identity Management a Office 365 Security & Center pro dodržování předpisů. Další informace o oprávněních sady Office 365 jsou k dispozici na stránce [oprávnění v centru zabezpečení & dodržování předpisů](/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

V | Může
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Všechna oprávnění role čtenář zabezpečení<br>Zobrazení, zkoumání a reakce na výstrahy zabezpečení hrozeb
Azure AD Identity Protection | Všechna oprávnění role čtenář zabezpečení<br>Kromě toho možnost provádět všechny operace centra služby Identity Protection s výjimkou resetování hesel a konfigurace e-mailů s výstrahami.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Všechna oprávnění role čtenář zabezpečení
[Office 365 Security & – centrum dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Všechna oprávnění role čtenář zabezpečení<br>Zobrazení, zkoumání a reakce na výstrahy zabezpečení
Ochrana ATP a EDR v programu Windows Defender | Všechna oprávnění role čtenář zabezpečení<br>Zobrazení, zkoumání a reakce na výstrahy zabezpečení
[Intune](/intune/role-based-access-control) | Všechna oprávnění role čtenář zabezpečení
[Cloud App Security](/cloud-app-security/manage-admins) | Všechna oprávnění role čtenář zabezpečení
[Stav služby Microsoft 365](/office365/enterprise/view-service-health) | Zobrazit stav služby Microsoft 365 Services

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti v protokolech auditu, včetně privilegovaných vlastností. |
> | Microsoft. Directory/cloudAppSecurity/allProperties/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Microsoft Cloud App Security |
> | Microsoft. Directory/identityProtection/allProperties/allTasks | Vytvořit a odstranit všechny prostředky a číst a aktualizovat standardní vlastnosti v Azure AD Identity Protection |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/Read | Čtení všech prostředků v Privileged Identity Management |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování. |
> | Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti v sestavách přihlášení, včetně privilegovaných vlastností. |
> | Microsoft. Azure. advancedThreatProtection/allEntities/allTasks | Spravovat všechny aspekty rozšířené ochrany před internetovými útoky Azure |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Intune/allEntities/Read | Čtení všech prostředků v Microsoft Intune |
> | Microsoft. Office 365. securityComplianceCenter/allEntities/allTasks | Vytváření a odstraňování všech prostředků a čtení a aktualizace standardních vlastností v centru Microsoft 365 Security and dodržování předpisů |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Windows. defenderAdvancedThreatProtection/allEntities/allTasks | Správa všech aspektů programu Microsoft Defender pro koncový bod |

## <a name="security-reader"></a>Čtecí modul zabezpečení

Uživatelé s touto rolí mají globální přístup jen pro čtení k funkcím souvisejícím se zabezpečením, včetně všech informací v Microsoft 365 Security Center, Azure Active Directory, Identity Protection, Privileged Identity Management, a také možnost číst Azure Active Directory sestav a protokolů auditu a v centru zabezpečení pro & 365 zabezpečení pro Office. Další informace o oprávněních sady Office 365 jsou k dispozici na stránce [oprávnění v centru zabezpečení & dodržování předpisů](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

V | Může
--- | ---
[Microsoft 365 Security Center](https://protection.office.com) | Zobrazení zásad souvisejících se zabezpečením napříč Microsoft 365 službami<br>Zobrazit bezpečnostní hrozby a výstrahy<br>Zobrazení sestav
Centrum služby Identity Protection | Čtení všech sestav zabezpečení a informací o nastavení pro funkce zabezpečení<br><ul><li>Ochrana proti nevyžádané poště<li>Šifrování<li>Prevence ztráty dat<li>Ochrana proti malwaru<li>Rozšířená ochrana před internetovými útoky<li>Ochrana proti podvodným zprávám<li>Pravidla toku pošty
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Má přístup jen pro čtení ke všem informacím, které jsou v Azure AD Privileged Identity Management: zásady a sestavy pro přiřazení rolí Azure AD a recenze zabezpečení.<br>**Nelze** se zaregistrovat k Azure AD Privileged Identity Management ani provádět žádné změny. V Privileged Identity Managementovém portálu nebo prostřednictvím prostředí PowerShell může osoba v této roli aktivovat další role (například správce globálního správce nebo privilegované role), pokud je pro ně uživatel oprávněný.
[Office 365 Security & – centrum dodržování předpisů](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Zobrazení zásad zabezpečení<br>Zobrazit a prozkoumat bezpečnostní hrozby<br>Zobrazení sestav
Ochrana ATP a EDR v programu Windows Defender | Zobrazit a prozkoumat výstrahy. Když zapnete řízení přístupu na základě role v ochraně ATP v programu Windows Defender, uživatelé s oprávněním jen pro čtení, jako je role čtenář zabezpečení Azure AD, ztratí přístup, dokud nebudou přiřazeni k roli ochrany ATP v programu Windows Defender.
[Intune](/intune/role-based-access-control) | Zobrazí informace o uživatelích, zařízeních, registraci, konfiguraci a aplikacích. Nemůže provádět změny v Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Má oprávnění jen pro čtení a může spravovat výstrahy.
[Azure Security Center](../../key-vault/managed-hsm/built-in-roles.md) | Může zobrazit doporučení a výstrahy, zobrazit zásady zabezpečení, zobrazit stavy zabezpečení, ale nemůže provádět změny.
[Stav služby Microsoft 365](/office365/enterprise/view-service-health) | Zobrazit stav služby Microsoft 365 Services

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/auditLogs/allProperties/Read | Načte všechny vlastnosti v protokolech auditu, včetně privilegovaných vlastností. |
> | Microsoft. Directory/bitlockerKeys/Key/Read | Číst metadata a klíč nástroje BitLocker na zařízeních |
> | Microsoft. Directory/entitlementManagement/allProperties/Read | Čtení všech vlastností v Azure AD – Správa nároků |
> | Microsoft. Directory/identityProtection/allProperties/Read | Čtení všech prostředků v Azure AD Identity Protection |
> | Microsoft. Directory/policies/Standard/Read | Číst základní vlastnosti zásad |
> | Microsoft. Directory/policies/Owners/Read | Čtení vlastníků zásad |
> | Microsoft. Directory/policies/policyAppliedTo/Read | Číst zásady. vlastnost policyAppliedTo |
> | Microsoft. Directory/conditionalAccessPolicies/Standard/Read | Číst podmíněný přístup pro zásady |
> | Microsoft. Directory/conditionalAccessPolicies/Owners/Read | Přečtěte si vlastníky zásad podmíněného přístupu. |
> | Microsoft. Directory/conditionalAccessPolicies/policyAppliedTo/Read | Přečtěte si vlastnost "použito na" pro zásady podmíněného přístupu. |
> | Microsoft. Directory/privilegedIdentityManagement/allProperties/Read | Čtení všech prostředků v Privileged Identity Management |
> | Microsoft. Directory/provisioningLogs/allProperties/Read | Načte všechny vlastnosti protokolů zřizování. |
> | Microsoft. Directory/signInReports/allProperties/Read | Načte všechny vlastnosti v sestavách přihlášení, včetně privilegovaných vlastností. |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Office 365. protectionCenter/allEntities/Standard/Read | Načte standardní vlastnosti všech prostředků v centrech zabezpečení a dodržování předpisů. |
> | Microsoft. Office 365. protectionCenter/attackSimulator/datové části/allProperties/Read | Čtení všech vlastností datových částí útoku v simulátoru útoku |
> | Microsoft. Office 365. protectionCenter/attackSimulator/Reports/allProperties/Read | Přečtěte si sestavy pro simulace útoků útoku a související školení. |
> | Microsoft. Office 365. protectionCenter/attackSimulator/simulace/allProperties/číst | Přečtěte si všechny vlastnosti šablon simulace útoků v simulátoru útoku. |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="service-support-administrator"></a>Správce služby Service support

Uživatelé s touto rolí můžou otevřít žádosti o podporu od Microsoftu pro Azure a Microsoft 365 služby a zobrazit řídicí panel služby a Centrum zpráv v [centru pro správu](https://admin.microsoft.com) [Azure Portal](https://portal.azure.com) a Microsoft 365. Další informace najdete v informacích [o rolích správce](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Dříve byla tato role v [Azure Portal](https://portal.azure.com) a v [centru pro správu Microsoft 365](https://admin.microsoft.com)se nazývá "Správce služby". Přejmenovali jsme ho na správce služby Service support, aby se v Microsoft Graph API, Azure AD Graph API a Azure AD PowerShell rovnal s názvem existujícího.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. Network/Performance/allProperties/Read | Čtení všech vlastností výkonu sítě v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="sharepoint-administrator"></a>Správce služby SharePoint

Uživatelé s touto rolí mají globální oprávnění v rámci služby Microsoft SharePoint Online, pokud je tato služba k dispozici, a také možnost vytvářet a spravovat všechny Microsoft 365 skupiny, spravovat lístky podpory a monitorovat stav služby. Další informace najdete v informacích [o rolích správce](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako "Správce služby SharePoint". Je to "Správce služby SharePoint" v [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Tato role také uděluje vymezená oprávnění Microsoft Graph rozhraní API pro Microsoft Intune a umožňuje tak správu a konfiguraci zásad souvisejících se zdroji na SharePointu a OneDrive.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/groups. Unified/Create | Vytvoření skupin Microsoft 365 s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/groups. Unified/DELETE | Odstranit skupiny Microsoft 365 s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Directory/groups. Unified/Restore | Obnovit Microsoft 365 skupiny |
> | Microsoft. Directory/groups. Unified/Basic/Update | Aktualizace základních vlastností u Microsoft 365 skupin s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Directory/groups. Unified/Members/Update | Aktualizace členů skupin Microsoft 365 s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Directory/groups. Unified/Owners/Update | Aktualizace vlastníků skupin Microsoft 365 s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. Network/Performance/allProperties/Read | Čtení všech vlastností výkonu sítě v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. sharePoint/allEntities/allTasks | Vytvářet a odstraňovat všechny prostředky a číst a aktualizovat standardní vlastnosti v SharePointu |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Čtení sestav využití Office 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="skype-for-business-administrator"></a>Správce Skypu pro firmy

Uživatelé s touto rolí mají globální oprávnění v rámci Microsoft Skype pro firmy, když je služba přítomná, a také spravovat atributy uživatelů pro Skype v Azure Active Directory. Kromě toho tato role uděluje možnost spravovat lístky podpory a monitorovat stav služeb a přistupovat k týmům a centru pro správu Skypu pro firmy. Účet musí být také licencován pro týmy nebo nemůže spustit rutiny prostředí PowerShell pro týmy. Další informace najdete v části informace o licencích [pro role správce Skypu pro firmy](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) a týmy na [webu Skype pro firmy a v doplňku pro licencování Microsoft Teams](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing) .

> [!NOTE]
> V rozhraní Microsoft Graph API a Azure AD PowerShell je tato role označená jako "Správce služby Lync". Ve [Azure Portal](https://portal.azure.com/)je to správce Skypu pro firmy.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Čtení sestav využití Office 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="teams-administrator"></a>Správce týmů

Uživatelé v této roli můžou spravovat všechny aspekty úloh Microsoft Teams prostřednictvím Microsoft Teams & centra pro správu Skypu pro firmy a příslušné moduly PowerShellu. Mezi další oblasti patří i všechny nástroje pro správu, které se týkají telefonního subsystému, zasílání zpráv, schůzek a týmů. Tato role navíc uděluje možnost vytvářet a spravovat všechny Microsoft 365 skupiny, spravovat lístky podpory a monitorovat stav služby.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Čtení skrytých členů skupiny |
> | Microsoft. Directory/groups. Unified/Create | Vytvoření skupin Microsoft 365 s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/groups. Unified/DELETE | Odstranit skupiny Microsoft 365 s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Directory/groups. Unified/Restore | Obnovit Microsoft 365 skupiny |
> | Microsoft. Directory/groups. Unified/Basic/Update | Aktualizace základních vlastností u Microsoft 365 skupin s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Directory/groups. Unified/Members/Update | Aktualizace členů skupin Microsoft 365 s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Directory/groups. Unified/Owners/Update | Aktualizace vlastníků skupin Microsoft 365 s vyloučením skupin, které lze přiřadit rolím |
> | Microsoft. Directory/servicePrincipals/managePermissionGrantsForGroup. Microsoft-All-Application-Permissions | Udělení instančního objektu s přímým přístupem k datům skupiny |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. Network/Performance/allProperties/Read | Čtení všech vlastností výkonu sítě v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Čtení sestav využití Office 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |
> | Microsoft. Teams/allEntities/allProperties/allTasks | Správa všech prostředků v týmech |

## <a name="teams-communications-administrator"></a>Správce komunikace týmů

Uživatelé v této roli můžou spravovat aspekty úloh Microsoft Teams souvisejících se službou Voice & telefonního subsystému. To zahrnuje nástroje pro správu přiřazení telefonního čísla, zásady hlasu a schůzky a úplný přístup ke sadě nástrojů pro analýzu volání.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. usageReports/allEntities/allProperties/Read | Čtení sestav využití Office 365 |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |
> | Microsoft. Teams/callQuality/allProperties/Read | Číst všechna data na řídicím panelu kvality volání (CQD) |
> | Microsoft. Teams/schůze/allProperties/allTasks | Správa schůzek, včetně zásad schůzky, konfigurací a konferenčních mostů |
> | Microsoft. Teams/Voice/allProperties/allTasks | Správa hlasu včetně zásad volání a inventáře a přiřazení telefonního čísla |

## <a name="teams-communications-support-engineer"></a>Týmy Communications support inženýr

Uživatelé v této roli můžou řešit problémy s komunikací v rámci Microsoft Teams & Skype pro firmy pomocí nástrojů pro řešení potíží s uživatelem v Microsoft Teams & v centru pro správu Skypu pro firmy. Uživatelé v této roli mohou zobrazit úplné informace o záznamech volání pro všechny účastnící se účastníky. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |
> | Microsoft. Teams/callQuality/allProperties/Read | Číst všechna data na řídicím panelu kvality volání (CQD) |

## <a name="teams-communications-support-specialist"></a>Týmy Communications support specialisty

Uživatelé v této roli můžou řešit problémy s komunikací v rámci Microsoft Teams & Skype pro firmy pomocí nástrojů pro řešení potíží s uživatelem v Microsoft Teams & v centru pro správu Skypu pro firmy. Uživatelé v této roli můžou zobrazit jenom podrobnosti o uživateli ve volání pro konkrétního uživatele, který si vyhledali. Tato role nemá přístup k zobrazení, vytvoření nebo správě lístků podpory.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. skypeForBusiness/allEntities/allTasks | Umožňuje spravovat všechny aspekty Online Skypu pro firmy. |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |
> | Microsoft. Teams/callQuality/Standard/Read | Číst základní data na řídicím panelu kvality volání (CQD) |

## <a name="teams-devices-administrator"></a>Správci zařízení Teams

Uživatelé s touto rolí můžou spravovat [zařízení s certifikací pro týmy](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) z centra pro správu týmů. Tato role umožňuje zobrazit všechna zařízení na jednom první pohled, s možností vyhledávání a filtrování zařízení. Uživatel může zkontrolovat podrobnosti každého zařízení, včetně přihlášeného účtu, a vytvořit a model zařízení. Uživatel může změnit nastavení na zařízení a aktualizovat verze softwaru. Tato role neuděluje oprávnění kontrolovat aktivity týmů a zavolá kvalitu zařízení.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |
> | Microsoft. Teams/Devices/Standard/Read | Správa všech aspektů zařízení, která jsou certifikována pro týmy včetně zásad konfigurace |

## <a name="usage-summary-reports-reader"></a>Čtečka sestav Souhrn využití

Uživatelé s touto rolí mají přístup ke agregovaným datům na úrovni tenanta a k souvisejícím přehledům v centru pro správu Microsoft 365 pro využití a zvýšení produktivity, ale nemají přístup k podrobnostem na úrovni uživatele nebo přehledům. V centru pro správu Microsoft 365 pro tyto dvě sestavy rozlišujeme mezi agregovanými daty úrovně tenanta a podrobnostmi na úrovni uživatele. Tato role poskytuje dodatečnou vrstvu ochrany na jednotlivých uživatelem identifikovatelných dat, která požadovali zákazníci i právní týmy.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Office 365. Network/Performance/allProperties/Read | Čtení všech vlastností výkonu sítě v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. usageReports/allEntities/Standard/Read | Číst agregované sestavy využití Office 365 na úrovni tenanta |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="user-administrator"></a>Správce uživatelů

Uživatelé s touto rolí můžou vytvářet uživatele a spravovat všechny aspekty uživatelů s některými omezeními (viz tabulku) a můžou aktualizovat zásady vypršení platnosti hesla. Uživatelé s touto rolí můžou navíc vytvářet a spravovat všechny skupiny. Tato role také zahrnuje možnost vytvářet a spravovat zobrazení uživatelů, spravovat lístky podpory a monitorovat stav služby. Správci uživatelů nemají oprávnění ke správě některých uživatelských vlastností pro uživatele ve většině rolí správce. Uživatel s touto rolí nemá oprávnění ke správě vícefaktorového ověřování. Role, které jsou výjimkou tohoto omezení, jsou uvedeny v následující tabulce.

| Oprávnění správce uživatele | Poznámky |
| --- | --- |
| Vytváření uživatelů a skupin<br/>Vytvářet a spravovat zobrazení uživatelů<br/>Správa lístků podpory Office<br/>Aktualizace zásad vypršení platnosti hesla |  |
| Správa licencí<br/>Spravovat všechny vlastnosti uživatele kromě hlavního názvu uživatele | Platí pro všechny uživatele, včetně všech správců. |
| Odstranění a obnovení<br/>Zakázat a povolit<br/>Spravovat všechny vlastnosti uživatele včetně hlavního názvu uživatele<br/>Aktualizace (FIDO) – klíče zařízení | Platí pro uživatele, kteří nejsou správci, nebo v některé z následujících rolí:<ul><li>Správce helpdesku</li><li>Uživatel bez role</li><li>Správce uživatelů</li></ul> |
| Zrušit platnost obnovovacích tokenů<br/>Resetování hesla | Seznam rolí, které může správce uživatelů resetovat a devalidate aktualizovat tokeny, najdete v tématu oprávnění k [resetování hesla](#password-reset-permissions). |

> [!IMPORTANT]
> Uživatelé s touto rolí můžou měnit hesla pro lidi, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci uvnitř i mimo Azure Active Directory. Změna hesla uživatele může znamenat možnost předpokládat identitu a oprávnění tohoto uživatele. Například:
>
>- Registrace aplikace a vlastníci podnikových aplikací, kteří můžou spravovat přihlašovací údaje aplikací, které vlastní. Tyto aplikace můžou mít privilegovaná oprávnění ve službě Azure AD a jinde nejsou udělená správcům uživatelů. Prostřednictvím této cesty může správce uživatelů předpokládat identitu vlastníka aplikace a pak dále předpokládat identitu privilegované aplikace tím, že aktualizuje přihlašovací údaje pro aplikaci.
>- Vlastníci předplatného Azure, kteří můžou mít přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure.
>- Skupina zabezpečení a Microsoft 365 vlastníci skupiny, kteří mohou spravovat členství ve skupině. Tyto skupiny můžou udělit přístup k citlivým nebo soukromým informacím nebo kritické konfiguraci v Azure AD a jinde.
>- Správci v jiných službách mimo Azure AD, jako je Exchange Online, Centrum zabezpečení a dodržování předpisů pro Office a systémy lidských zdrojů.
>- Nesprávci jako vedoucí pracovníci, právní poradce a zaměstnanci lidských zdrojů, kteří mohou mít přístup k citlivým nebo soukromým informacím.

> [!div class="mx-tableFixed"]
> | Akce | Popis |
> | --- | --- |
> | Microsoft. Directory/appRoleAssignments/Create | Vytvoření přiřazení aplikační role |
> | Microsoft. Directory/appRoleAssignments/DELETE | Odstranit přiřazení rolí aplikace |
> | Microsoft. Directory/appRoleAssignments/Basic/Update | Aktualizace základních vlastností přiřazení rolí aplikace |
> | Microsoft. Directory/Contacts/Create | Vytvoření kontaktů |
> | Microsoft. Directory/Contacts/DELETE | Odstranit kontakty |
> | Microsoft. Directory/Contacts/Basic/Update | Aktualizace základních vlastností u kontaktů |
> | Microsoft. Directory/entitlementManagement/allProperties/allTasks | Vytváření a odstraňování prostředků a čtení a aktualizace všech vlastností v Azure AD – Správa nároků |
> | Microsoft. Directory/Groups/assignLicense | Přiřazení licencí k produktu skupinám pro licencování na základě skupin |
> | Microsoft. Directory/Groups/Create | Vytváření skupin s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/DELETE | Odstranit skupiny s výjimkou skupiny přiřazení role |
> | Microsoft. Directory/Groups/hiddenMembers/Read | Čtení skrytých členů skupiny |
> | Microsoft. Directory/Groups/reprocessLicenseAssignment | Opětovné zpracování přiřazení licencí pro licencování na základě skupin |
> | Microsoft. Directory/skupiny/obnovení | Obnovení odstraněných skupin |
> | Microsoft. Directory/Groups/Basic/Update | Aktualizovat základní vlastnosti u skupin s vyloučením skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/klasifikací/Update | Aktualizovat vlastnost klasifikace skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/dynamicMembershipRule/Update | Aktualizovat dynamické pravidlo členství skupin s výjimkou skupin, které lze přiřadit k rolím |
> | Microsoft. Directory/Groups/groupType/Update | Aktualizace vlastnosti groupType pro skupinu |
> | Microsoft. Directory/Groups/Members/Update | Aktualizace členů skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/onPremWriteBack/Update | Aktualizace skupin Azure Active Directory pro zápis do místního prostředí s využitím Azure AD Connect |
> | Microsoft. Directory/Groups/Owners/Update | Aktualizace vlastníků skupin bez skupin s přiřazením rolí |
> | Microsoft. Directory/Groups/Settings/Update | Aktualizovat nastavení skupin |
> | Microsoft. Directory/Groups/Visibility/Update | Aktualizuje vlastnost viditelnost skupin. |
> | Microsoft. Directory/oAuth2PermissionGrants/allProperties/allTasks | Umožňuje vytvářet a odstraňovat oprávnění OAuth 2,0 a číst a aktualizovat všechny vlastnosti. |
> | Microsoft. Directory/servicePrincipals/appRoleAssignedTo/Update | Aktualizovat přiřazení rolí instančního objektu |
> | Microsoft. Directory/Users/assignLicense | Správa uživatelských licencí |
> | Microsoft. Directory/uživatelé/vytvořit | Přidání uživatelů |
> | Microsoft. Directory/Users/DELETE | Odstranění uživatelů |
> | Microsoft. Directory/Users/Disable | Zakázat uživatele |
> | Microsoft. Directory/uživatelé/povolit | Povolit uživatele |
> | Microsoft. Directory/Users/inviteGuest | Pozvání uživatelů typu host |
> | Microsoft. Directory/Users/invalidateAllRefreshTokens | Vynutit odhlášení pomocí neověřování tokenů aktualizace uživatelů |
> | Microsoft. Directory/Users/reprocessLicenseAssignment | Znovu zpracovat přiřazení licencí pro uživatele |
> | Microsoft. Directory/Users/Restore | Obnovit odstraněné uživatele |
> | Microsoft. Directory/uživatelé/Basic/Update | Aktualizace základních vlastností u uživatelů |
> | Microsoft. Directory/uživatelé/správce/aktualizace | Správce aktualizací pro uživatele |
> | Microsoft. Directory/uživatelé/heslo/aktualizace | Resetovat hesla pro všechny uživatele |
> | Microsoft. Directory/uživatelé/userPrincipalName/Update | Aktualizovat hlavní název uživatele uživatelů |
> | Microsoft. Azure. serviceHealth/allEntities/allTasks | Čtení a konfigurace Azure Service Health |
> | Microsoft. Azure. supportTickets/allEntities/allTasks | Vytváření a Správa lístků podpory Azure |
> | Microsoft. Office 365. serviceHealth/allEntities/allTasks | Čtení a konfigurace Service Health v centru pro správu Microsoft 365 |
> | Microsoft. Office 365. supportTickets/allEntities/allTasks | Vytváření a správa žádostí o Microsoft 365 služby |
> | Microsoft. Office 365. WebPort/allEntities/Standard/Read | Přečte základní vlastnosti všech prostředků v centru pro správu Microsoft 365. |

## <a name="how-to-understand-role-permissions"></a>Pochopení oprávnění rolí

Schéma pro oprávnění bez omezení Microsoft Graph následující:

`<namespace>/<entity>/<propertySet>/<action>`

Například:

`microsoft.directory/applications/credentials/update`

| Element oprávnění | Description |
| --- | --- |
| namespace | Produkt nebo služba, které vystavují úkol a jsou součástí `microsoft` . Například všechny úlohy ve službě Azure AD používají `microsoft.directory` obor názvů. |
| entita | Logická funkce nebo komponenta vystavené službou v Microsoft Graph. Například Azure AD zveřejňuje uživatele a skupiny, OneNote zpřístupňuje poznámky a Exchange zveřejňuje poštovní schránky a kalendáře. Existuje speciální `allEntities` klíčové slovo pro zadání všech entit v oboru názvů. Tato funkce se často používá v rolích, které udělují přístup k celému produktu. |
| PropertySet – | Konkrétní vlastnosti nebo aspekty entity, pro které se uděluje přístup Například `microsoft.directory/applications/authentication/read` udělí možnost číst adresu URL odpovědi, adresu URL pro odhlášení a vlastnost implicitního toku u objektu aplikace v Azure AD.<ul><li>`allProperties` Určuje všechny vlastnosti entity, včetně privilegovaných vlastností.</li><li>`standard` určí společné vlastnosti, ale vyloučí privilegované objekty související s `read` akcí. Například `microsoft.directory/user/standard/read` umožňuje číst standardní vlastnosti, jako je veřejné telefonní číslo a e-mailová adresa, ale ne soukromé sekundární telefonní číslo nebo e-mailovou adresu, která se používá pro službu Multi-Factor Authentication.</li><li>`basic` určí společné vlastnosti, ale vyloučí privilegované objekty související s `update` akcí. Sada vlastností, které lze číst, se může lišit od toho, co můžete aktualizovat. To je důvod, proč existují `standard` a `basic` klíčová slova, která se mají odrážet.</li></ul> |
| action | Povolená operace, většinou je vytváření, čtení, aktualizace nebo odstranění (CRUD). Existuje speciální `allTasks` klíčové slovo pro určení všech výše uvedených možností (vytvoření, čtení, aktualizace a odstranění). |

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
Připojení zařízení | Zastaralé | [Dokumentace k zastaralým rolím](#deprecated-roles)
Správci zařízení | Zastaralé | [Dokumentace k zastaralým rolím](#deprecated-roles)
Uživatelé zařízení | Zastaralé | [Dokumentace k zastaralým rolím](#deprecated-roles)
Účty synchronizace adresářů | Nezobrazeno, protože by neměl být použit | [Dokumentace k účtům synchronizace adresářů](#directory-synchronization-accounts)
Uživatel typu host | Nezobrazeno, protože nemůže být použit | NA
Podpora partnerské vrstvy 1 | Nezobrazeno, protože by neměl být použit | [Dokumentace k podpoře partnerů Tier1](#partner-tier1-support)
Podpora partnerské úrovně 2 | Nezobrazeno, protože by neměl být použit | [Dokumentace k podpoře partnerů 2](#partner-tier2-support)
Omezený uživatel typu Host | Nezobrazeno, protože nemůže být použit | NA
Uživatel | Nezobrazeno, protože nemůže být použit | NA
Připojení zařízení na pracovišti | Zastaralé | [Dokumentace k zastaralým rolím](#deprecated-roles)

## <a name="password-reset-permissions"></a>Oprávnění pro resetování hesla

Záhlaví sloupců reprezentují role, které můžou resetovat hesla. Řádky tabulky obsahují role, pro které je možné resetovat heslo.

Heslo je možné resetovat. | Správce hesel | Správce helpdesku | Správce ověřování | Správce uživatelů | Správce privilegovaného ověřování | Globální správce
------ | ------ | ------ | ------ | ------ | ------ | ------
Správce ověřování | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Čtečky adresářů | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Globální správce | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Správce skupin | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Pozvánka hosta | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Správce helpdesku | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Čtenář centra zpráv | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Správce hesel | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Správce privilegovaného ověřování | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Správce privilegovaných rolí | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Čtečka sestav | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Uživatel (bez role správce) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Správce uživatelů | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Čtečka sestav Souhrn využití | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* Globální správce nemůže odebrat vlastní globální přiřazení správce. K tomu je potřeba zabránit situaci, kdy má organizace 0 globálních správců.

## <a name="next-steps"></a>Další kroky

- [Přiřazení rolí Azure AD k skupinám](groups-assign-role.md)
- [Vysvětlení různých rolí](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Přiřazení role správce předplatného Azure uživateli](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
