---
title: Nejčastější dotazy – Azure Active Directory Domain Services | Microsoft Docs
description: Nejčastější dotazy týkající se Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 509bd472fe5f930ac830b8b4016129f8acb67324
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612880"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Nejčastější dotazy
Tato stránka obsahuje odpovědi na nejčastější dotazy týkající se Azure Active Directory Domain Services. Udržujte kontrolu aktualizací.

## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží
Řešení běžných potíží s konfigurací nebo správou Azure AD Domain Services najdete v [Průvodci odstraňováním potíží](troubleshoot.md) .

## <a name="configuration"></a>Konfiguraci
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Můžu vytvořit více spravovaných domén pro jeden adresář služby Azure AD?
Ne. Pro jeden adresář služby Azure AD můžete vytvořit jenom jednu spravovanou doménu Azure AD Domain Services.  

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Můžu povolit Azure AD Domain Services v klasické virtuální síti?
Klasické virtuální sítě nejsou pro nová nasazení podporovaná. Existující spravované domény nasazené v klasických virtuálních sítích se pořád podporují.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Můžu Azure AD Domain Services ve virtuální síti Azure Resource Manager povolit?
Ano. Azure AD Domain Services lze povolit ve virtuální síti Azure Resource Manager. Klasické virtuální sítě Azure už nejsou podporované pro vytváření nových spravovaných domén.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Můžu migrovat existující spravovanou doménu z klasické virtuální sítě do Správce prostředků virtuální sítě?
Momentálně ne. Microsoft doručí mechanismus migrace stávající spravované domény z klasické virtuální sítě do Správce prostředků virtuální sítě v budoucnu.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Můžu Azure AD Domain Services povolit v předplatném Azure CSP (Cloud Solution Provider)?
Ano. Podívejte se, jak můžete povolit [Azure AD Domain Services v předplatných Azure CSP](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Můžu Azure AD Domain Services povolit ve federovaném adresáři služby Azure AD? Nesynchronizuji hodnoty hash hesel do služby Azure AD. Můžu povolit Azure AD Domain Services pro tento adresář?
Ne. Azure AD Domain Services potřebuje přístup k hodnotám hash hesla uživatelských účtů, aby bylo možné ověřovat uživatele pomocí protokolu NTLM nebo Kerberos. Ve federovaném adresáři se hodnoty hash hesel neukládají do adresáře služby Azure AD. Proto Azure AD Domain Services nefunguje s těmito adresáři služby Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Můžu Azure AD Domain Services zpřístupnit ve více virtuálních sítích v rámci svého předplatného?
Samotná služba přímo nepodporuje tento scénář. Vaše spravovaná doména je dostupná v jednom okamžiku jenom v jedné virtuální síti. Můžete ale nakonfigurovat připojení mezi několika virtuálními sítěmi a zpřístupnit Azure AD Domain Services jiným virtuálním sítím. Podívejte se, jak se můžete [připojit k virtuálním sítím v Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Můžu povolit Azure AD Domain Services používání PowerShellu?
Ano. Podívejte [se, jak povolit Azure AD Domain Services pomocí PowerShellu](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Můžu Azure AD Domain Services povolit použití šablony Správce prostředků?
Ne, v současné době není možné povolit Azure AD Domain Services pomocí šablony. Místo toho použijte PowerShell a podívejte se [na téma Jak povolit Azure AD Domain Services pomocí PowerShellu](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Můžu přidat řadiče domény do spravované domény Azure AD Domain Services?
Ne. Doména, kterou poskytuje Azure AD Domain Services, je spravovaná doména. Pro tuto doménu nemusíte zřizovat, konfigurovat ani jinak spravovat řadiče domény – tyto aktivity správy se poskytují jako služba od Microsoftu. Proto pro spravovanou doménu nelze přidat další řadiče domény (pro čtení i zápis nebo jen pro čtení).

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Můžou se uživatelé typu Host pozvat do adresáře použít Azure AD Domain Services?
Ne. Uživatelé typu Host, kteří se pozvali k adresáři Azure AD pomocí procesu pozvání [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) , se synchronizují do vaší Azure AD Domain Services spravované domény. Hesla pro tyto uživatele ale nejsou uložená ve vašem adresáři služby Azure AD. Proto Azure AD Domain Services nemá žádný způsob, jak synchronizovat hodnoty hash protokolu NTLM a Kerberos pro tyto uživatele do spravované domény. V důsledku toho se tito uživatelé nebudou moci přihlásit ke spravované doméně nebo připojit počítače ke spravované doméně.

## <a name="administration-and-operations"></a>Správa a operace
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Můžu se k řadiči domény pro spravovanou doménu připojit pomocí vzdálené plochy?
Ne. Nemáte oprávnění k připojení k řadičům domény pro spravovanou doménu přes vzdálenou plochu. Členové skupiny Správci AAD DC mohou spravovat spravovanou doménu pomocí nástrojů pro správu služby AD, jako je například centrum pro správu služby Active Directory (ADAC) nebo prostředí AD PowerShell. Tyto nástroje se instalují pomocí funkce Nástroje pro vzdálenou správu serveru na Windows serveru, který je připojený ke spravované doméně.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Povolil (a) Azure AD Domain Services. Jaký uživatelský účet mám použít k připojení počítačů do domény k této doméně?
Členové skupiny pro správu AAD DC Administrators můžou počítače připojit k doméně. Členové této skupiny navíc udělují přístup ke vzdálené ploše počítačům, které jsou připojené k doméně.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Mám oprávnění správce domény pro spravovanou doménu, kterou poskytuje Azure AD Domain Services?
Ne. Neudělujete oprávnění správce ve spravované doméně. Oprávnění správce domény a Enterprise Administrators nejsou k dispozici pro použití v rámci domény. Členům skupiny Domain Administrators nebo Enterprise Administrators ve vaší místní službě Active Directory se taky ve spravované doméně neudělují oprávnění správce domény nebo rozlehlé sítě.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Je možné upravit členství ve skupinách pomocí protokolu LDAP nebo jiných nástrojů pro správu služby AD ve spravovaných doménách?
Ne. Členství ve skupinách nelze upravovat v doménách, které jsou službou Azure AD Domain Services. Totéž platí pro atributy uživatele. Můžete ale změnit členství ve skupině nebo atributy uživatele buď v Azure AD, nebo v místní doméně. Tyto změny se automaticky synchronizují s Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak dlouho trvá, aby se změny v adresáři služby Azure AD zobrazily ve spravované doméně?
Změny provedené v adresáři Azure AD pomocí uživatelského rozhraní Azure AD nebo PowerShellu se synchronizují do vaší spravované domény. Tento proces synchronizace běží na pozadí. Po dokončení počáteční synchronizace obvykle trvá přibližně 20 minut, než se změny provedené ve službě Azure AD projeví ve spravované doméně.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Můžu roztáhnout schéma spravované domény, které poskytuje Azure AD Domain Services?
Ne. Schéma je spravováno společností Microsoft pro spravovanou doménu. Azure AD Domain Services nepodporuje rozšíření schématu.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Můžu v mé spravované doméně upravovat nebo přidávat záznamy DNS?
Ano. Členům skupiny AAD DC Administrators jsou udělena oprávnění správce DNS, aby bylo možné upravovat záznamy DNS ve spravované doméně. Ke správě DNS můžou používat konzolu Správce DNS na počítači, na kterém běží Windows Server, který je připojený ke spravované doméně. Pokud chcete použít konzolu Správce DNS, nainstalujte nástroj Server DNS, který je součástí volitelné funkce Nástroje pro vzdálenou správu serveru na serveru. Další informace o [nástrojích pro správu, monitorování a řešení potíží DNS](https://technet.microsoft.com/library/cc753579.aspx) jsou k dispozici na webu TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Jaké jsou zásady životního cyklu hesel ve spravované doméně?
Výchozí doba života hesla ve spravované doméně Azure AD Domain Services je 90 dní. Doba života hesla není synchronizovaná s životností hesla nakonfigurovanou v Azure AD. Proto můžete mít situaci, kdy vyprší platnost hesel uživatelů ve vaší spravované doméně, ale ve službě Azure AD stále platí. V takových scénářích potřebují uživatelé změnit heslo v Azure AD a nové heslo se synchronizuje do vaší spravované domény. K vaší spravované doméně se navíc nesynchronizují atributy "Password-not-vypršení platnosti" a "User-:-Change-Password-on-Next-Logon" pro uživatelské účty.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Poskytuje Azure AD Domain Services ochranu před uzamčením účtu AD?
Ano. Pět neplatných pokusů o zadání hesla ve spravované doméně způsobí, že se uživatelský účet zamkne po dobu 30 minut. Po 30 minutách se uživatelský účet automaticky odemkne. Neplatný počet pokusů o zadání hesla ve spravované doméně nezamkne uživatelský účet ve službě Azure AD. Uživatelský účet je uzamčený pouze v rámci vaší Azure AD Domain Services spravované domény.

## <a name="billing-and-availability"></a>Fakturace a dostupnost
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Je Azure AD Domain Services placená služba?
Ano. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Máte k dispozici bezplatnou zkušební verzi služby?
Tato služba je součástí bezplatné zkušební verze pro Azure. Můžete si zaregistrovat [bezplatnou měsíční zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Můžu pozastavit Azure AD Domain Services spravovanou doménu? 
Ne. Po povolení spravované domény Azure AD Domain Services je tato služba dostupná v rámci vybrané virtuální sítě, dokud nezakážete nebo neodstraníte spravovanou doménu. Neexistuje žádný způsob, jak službu pozastavit. Fakturace bude pokračovat po hodinách, dokud neodstraníte spravovanou doménu.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Je možné převzetí služeb při selhání Azure AD Domain Services do jiné oblasti pro událost zotavení po havárii?
Ne.  Azure AD Domain Services aktuálně neposkytuje geograficky redundantní model nasazení. Je omezená na jednu virtuální síť v oblasti Azure. Pokud chcete využívat více oblastí Azure, musíte spustit řadiče Doména služby Active Directory na virtuálních počítačích Azure s IaaS.  Pokyny k architektuře najdete [tady](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Můžu Azure AD Domain Services jako součást sady Enterprise Mobility Suite (EMS)? Potřebuji Azure AD Premium používat Azure AD Domain Services?
Ne. Azure AD Domain Services je služba Azure s průběžnými platbami a není součástí EMS. Azure AD Domain Services lze použít se všemi edicemi služby Azure AD (Free a Premium). Poplatky se účtují po hodinách, v závislosti na využití.

### <a name="what-azure-regions-is-the-service-available-in"></a>K jakým oblastem Azure je služba dostupná?
Seznam oblastí Azure, kde je Azure AD Domain Services k dispozici, najdete na stránce [služby Azure podle oblasti](https://azure.microsoft.com/regions/#services/) .
