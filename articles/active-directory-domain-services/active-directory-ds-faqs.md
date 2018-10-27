---
title: Nejčastější dotazy – Azure Active Directory Domain Services | Dokumentace Microsoftu
description: Nejčastější dotazy k Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: c2f10b1bf3b61577a2b0616be6fa50f7639559ba
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158907"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Nejčastější dotazy (FAQ)
Tato stránka odpovědi na nejčastější dotazy týkající se Azure Active Directory Domain Services. Kontrolovat novinky.

## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží
Odkazovat [Průvodce odstraňováním potíží](active-directory-ds-troubleshooting.md) pro řešení běžných potíží s konfigurací nebo Správa služby Azure AD Domain Services.

## <a name="configuration"></a>Konfigurace
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Můžete vytvořit několik spravovaných domén pro jeden adresář Azure AD?
Ne. Můžete vytvořit jen jednu spravovanou doménu obsluhovány pomocí Azure AD Domain Services pro jeden adresář Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Můžete povolit Azure AD Domain Services ve virtuální síti Azure Resource Manageru?
Ano. Azure AD Domain Services můžete povolit ve virtuální síti Azure Resource Manageru. Klasické virtuální sítě Azure již nejsou podporovány pro vytvoření nové spravované domény.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Můžu migrovat své existující spravovanou doménu z klasické virtuální sítě k virtuální síti správce prostředků?
Není aktuálně. Microsoft bude poskytovat mechanismus pro migraci svoji existující spravovanou doménu z klasické virtuální sítě k virtuální síti správce prostředků v budoucnu.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Můžete povolit Azure AD Domain Services v rámci předplatného Azure CSP (Cloud Solution Provider)?
Ano. Zobrazit, jak můžete zajistit [Azure AD Domain Services v předplatných Azure CSP](active-directory-ds-csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Můžete povolit Azure AD Domain Services ve federované Azure AD directory? Můžu nesynchronizovat hodnot hash hesel do služby Azure AD. Můžete povolit pro tento adresář Azure AD Domain Services?
Ne. Azure AD Domain Services potřebuje přístup k hodnoty hash hesla uživatelských účtů, ověřování uživatelů pomocí ověřování protokolem NTLM nebo Kerberos. Ve federované adresáři nejsou uložené hodnoty hash hesel v adresáři Azure AD. Azure AD Domain Services se proto nefunguje s těchto adresářů služby Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Je možné vytvořit Azure AD Domain Services k dispozici v několika virtuálními sítěmi v rámci předplatného?
Samotné služby přímo nepodporuje tento scénář. Vaše spravovaná doména je k dispozici v pouze jedné virtuální síti současně. Můžete ale nakonfigurovat připojení mezi virtuálními sítěmi k vystavení služby Azure AD Domain Services k jiným virtuálním sítím. Naleznete v tématu Jak [propojení virtuálních sítí v Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Můžete povolit Azure AD Domain Services pomocí Powershellu?
Ano. Zobrazit [jak povolit Azure AD Domain Services pomocí prostředí PowerShell](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Můžete povolit Azure AD Domain Services pomocí šablony Resource Manageru?
Ne, není v tuto chvíli možné si povolit Azure AD Domain Services pomocí šablony. Místo toho prostředí PowerShell, najdete v tématu [jak povolit Azure AD Domain Services pomocí prostředí PowerShell](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Můžete přidat řadiče domény k spravované doméně služby Azure AD Domain Services?
Ne. Doména poskytovaných službou Azure AD Domain Services je spravované domény. Není potřeba zřizovat, konfigurovat nebo jinak spravovat řadiče domény pro tuto doménu – tyto aktivity správy jsou k dispozici jako služba od Microsoftu. Proto nelze přidat další řadiče domény (čtení a zápis nebo jen pro čtení) pro spravovanou doménu.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Můžete k adresáře pozváni uživatelé typu Host pomocí Azure AD Domain Services?
Ne. Uživatelé typu Host pozvaný do vašeho adresáře Azure AD pomocí [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) proces pozvánky jsou synchronizovány do spravované domény služby Azure AD Domain Services. Hesla pro tyto uživatele ale nejsou uložené v adresáři služby Azure AD. Proto Azure AD Domain Services nemá žádný způsob, jak synchronizovat NTLM a Kerberos hodnot hash pro tyto uživatele do spravované domény. V důsledku toho tato uživatelé nemohou přihlásit ke spravované doméně nebo připojení počítače k spravované doméně.

## <a name="administration-and-operations"></a>Operace a Správa
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Můžu připojit k řadiči domény pro spravované domény pomocí vzdálené plochy?
Ne. Nemáte oprávnění k připojení k řadičům domény pro spravovanou doménu přes vzdálenou plochu. Členové skupiny "Správci AAD DC" můžete spravovat spravované domény pomocí nástroje pro správu AD jako Centra správy Active Directory (ADAC) nebo Powershellu AD. Tyto nástroje jsou nainstalovány na serveru Windows k spravované doméně pomocí funkce 'Nástroje pro vzdálenou správu'.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Můžu jste povolili službě Azure AD Domain Services. Jaký účet používat na domény připojit počítače k této doméně?
Členové skupiny pro správu "Správci AAD DC" můžete počítače připojení k doméně. Členové této skupiny jsou navíc udělen přístup ke vzdálené ploše na počítače, které byly připojené k doméně.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Je nutné oprávnění správce domény pro spravovanou doménu poskytovaných službou Azure AD Domain Services?
Ne. Nejsou udělena oprávnění správce pro spravovanou doménu. "Správce" a "Enterprise Administrator" oprávnění nejsou k dispozici pro použití v rámci domény. Správce domény nebo skupiny správce rozlehlé sítě v místním Active Directory nejsou poskytnuta oprávnění správce domény/enterprise ve spravované doméně.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Můžete upravit členství ve skupinách na spravované domény pomocí protokolu LDAP nebo jiné nástroje pro správu AD?
Ne. V doménách, které jsou obsluhovány pomocí Azure AD Domain Services nelze upravit členství ve skupinách. To samé platí pro atributy uživatele. Můžete však změnit atributy uživatele nebo členství ve skupinách ve službě Azure AD nebo v místní doméně. Tyto změny se automaticky synchronizovat s Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak dlouho trvá změny můžu provést adresáře Azure AD mají být zobrazeny v mé spravovanou doménu?
Změny provedené v adresáři služby Azure AD pomocí Azure AD uživatelského rozhraní nebo Powershellu jsou synchronizovány do spravované domény. Tento proces synchronizace běží na pozadí. Po dokončení počáteční synchronizace se obvykle trvá přibližně 20 minut, než se změny provedené ve službě Azure AD, se projevovat ve vaší spravované domény.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Můžete rozšířit schéma této spravované doméně poskytovaných službou Azure AD Domain Services?
Ne. Schéma je spravována microsoftem pro spravovanou doménu. Rozšíření schématu nejsou podporovány službou Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Můžete upravit nebo přidat záznamy DNS v mé spravovanou doménu?
Ano. Členové skupiny "Správci AAD DC" jsou udělena oprávnění ' Správce DNS, k úpravě záznamů DNS ve spravované doméně. Ke správě DNS, můžete použít konzolu Správce DNS na počítači se systémem Windows Server připojený ke spravované doméně. Pomocí konzoly Správce DNS, nainstalujte "Nástroje serveru DNS", která je součástí volitelná funkce 'Nástroje pro vzdálenou správu' na serveru. Další informace o [nástroje pro správu, monitorování a řešení potíží s DNS](https://technet.microsoft.com/library/cc753579.aspx) je k dispozici na webu TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Co jsou zásady životnosti hesel ve spravované doméně?
Výchozí doba života heslo v doméně služby Azure AD služby spravované domény je 90 dní. Tato doba platnosti hesla není synchronizován s životností hesla nakonfigurované ve službě Azure AD. Proto je nutné situace, kdy platnost ve vaší spravované doméně hesla uživatelů, ale stále platné ve službě Azure AD. V takových případech uživatelé potřebují ke změně hesla ve službě Azure AD a nové heslo bude synchronizovat do spravované domény. Kromě toho "-neposkytuje not-konec platnosti hesla" a "user-must-change-password-at-next-logon" atributy pro uživatelské účty nejsou synchronizovány do spravované domény.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services poskytuje ochranu před uzamčením účtu AD?
Ano. Pět neplatné pokusy o přihlášení během 2 minut ve spravované doméně způsobit, že uživatelský účet v uzamčena na dobu 30 minut. Po 30 minutách je automaticky odemkne uživatelský účet. Neplatné pokusy o hesla ve spravované doméně nepoužívejte zámky na uživatelský účet ve službě Azure AD. Uživatelský účet je uzamčen pouze v rámci vaší domény spravované služby Azure AD Domain Services.

## <a name="billing-and-availability"></a>Fakturace a dostupnosti
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Je, že Azure AD Domain Services placené služby?
Ano. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Je k dispozici k bezplatné zkušební verzi služby?
Tato služba je součástí bezplatné zkušební verze pro Azure. Můžete si zaregistrovat [bezplatné měsíční zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Můžete pozastavit spravované domény služby Azure AD Domain Services? 
Ne. Jakmile povolíte spravované domény služby Azure AD Domain Services, je služba dostupná ve vámi zvolené virtuální síti, dokud je zakázat/odstranit spravovanou doménu. Neexistuje žádný způsob, jak službu pozastavit. Fakturace pokračuje po hodinách, dokud odstranit spravovanou doménu.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Je možné převzetí služeb při selhání do jiné oblasti pro zotavení po Havárii událostí Azure AD Domain Services?
Ne.  Azure AD Domain Services aktuálně neposkytuje geograficky redundantní nasazení modelu. Je omezený na jednu virtuální síť v oblasti Azure. Pokud chcete využívat víc oblastí Azure, musíte spustit řadičů domény služby Active Directory na virtuálních počítačích Azure IaaS.  Najdete doprovodné materiály k architektuře [tady](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>K dispozici v Azure AD Domain Services jako součást sady Enterprise Mobility Suite (EMS)? Je třeba Azure AD Premium k použití služby Azure AD Domain Services?
Ne. Azure AD Domain Services je služba Azure s průběžnými platbami a není součástí EMS. Azure AD Domain Services je možné ve všech edicích služby Azure AD (Free, Basic a, Premium). Se účtují po hodinách účtujeme, v závislosti na využití.

### <a name="what-azure-regions-is-the-service-available-in"></a>Jaké oblasti Azure, které je služba k dispozici v?
Odkazovat [služeb Azure podle oblasti](https://azure.microsoft.com/regions/#services/) stránku, aby zobrazil seznam oblastí Azure, kde je k dispozici služba Azure AD Domain Services.
