---
title: "Nejčastější dotazy – Azure Active Directory Domain Services | Microsoft Docs"
description: "Časté otázky k Azure Active Directory Domain Services"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: maheshu
ms.openlocfilehash: 1cfd0570315d5a1c6587ade164edf0a837453406
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Časté otázky (FAQ)
Tato stránka odpovědi časté otázky o Azure Active Directory Domain Services. Kontrolovat zpět aktualizací.

## <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží
Odkazovat [Průvodce odstraňováním potíží](active-directory-ds-troubleshooting.md) pro řešení běžných problémů při konfiguraci nebo jejich správě Azure AD Domain Services.

## <a name="configuration"></a>Konfigurace
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Můžete vytvořit více spravovaných domén pro jeden adresář Azure AD?
Ne. Můžete vytvořit pouze jeden spravované doméně obsluhovány pomocí Azure AD Domain Services pro jeden adresář Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Můžete povolit Azure AD Domain Services ve virtuální síti Azure Resource Manager?
Ano. Azure AD Domain Services se dají povolit v virtuální síť Azure Resource Manager. Klasické virtuální sítě Azure již nejsou podporovány pro vytvoření nové spravované domény.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Můžete migrovat mé existující spravované domény z klasickou virtuální síť k virtuální síti Resource Manager?
Aktuálně nepodporuje. Microsoft bude poskytovat mechanismus k migraci stávající spravované domény z klasickou virtuální síť k virtuální síti Resource Manager v budoucnu.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Můžete povolit Azure AD Domain Services v předplatné Azure CSP (Cloud Solution Provider)?
Ano. V tématu jak můžete povolit [Azure AD Domain Services v rámci předplatných Azure CSP](active-directory-ds-csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Můžete povolit funkci Azure AD Domain Services ve federované Azure AD adresáře? I nesynchronizovat hodnot hash hesel do služby Azure AD. Můžete povolit pro tento adresář Azure AD Domain Services?
Ne. Azure AD Domain Services, potřebuje přístup k hodnoty hash hesla uživatelských účtů, ověřuje uživatele pomocí protokolu NTLM nebo Kerberos. Federované adresáře hodnot hash hesel nejsou uložené v adresáři služby Azure AD. Azure AD Domain Services se proto nefunguje s takové adresáře Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Můžete vytvořit Azure AD Domain Services k dispozici v několika virtuálními sítěmi v rámci Moje předplatné?
Služba sama přímo nepodporuje tento scénář. Najednou je k dispozici v pouze jednu virtuální síť vaší spravované domény. Můžete ale nakonfigurovat připojení mezi více virtuálních sítí, která zveřejňuje Azure AD Domain Services pro jiné virtuální sítě. V tématu jak můžete [propojit virtuální sítě v Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Můžete povolit Azure AD Domain Services pomocí prostředí PowerShell?
Ano. V tématu [jak povolit Azure AD Domain Services pomocí prostředí PowerShell](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Můžete povolit pomocí šablony správce prostředků Azure AD Domain Services?
Ano. V tématu [jak povolit Azure AD Domain Services pomocí prostředí PowerShell](active-directory-ds-enable-using-powershell.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Můžete přidávat řadiče domény k spravované doméně služby Azure AD Domain Services?
Ne. Domény, které poskytuje Azure AD Domain Services je spravovaná doména. Není potřeba zřizovat, konfigurovat nebo jinak spravovat řadiče domény pro tuto doménu - tyto aktivity správy jsou poskytovány jako služba společnosti Microsoft. Proto nelze přidat další řadiče domény (pro čtení a zápis nebo jen pro čtení) pro spravovanou doménu.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Můžete použít uživatele typu Host pozvánku, abyste složku adresář Azure AD Domain Services?
Ne. Uživatele typu Host pozvánku, abyste pomocí adresář Azure AD [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) pozvání procesu jsou sycned do vaší spravované domény služby Azure AD Domain Services. Hesla pro tyto uživatele však nejsou uložené v adresáři služby Azure AD. Proto služba Azure AD Domain Services má žádný způsob, jak synchronizovat NTLM a Kerberos vytvoří hodnotu hash pro tyto uživatele do vaší spravované domény. V důsledku toho nelze takové uživatelé přihlášení k spravované doméně nebo připojení počítače k spravované doméně.

## <a name="administration-and-operations"></a>Operace a Správa
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Možné připojit k řadiči domény pro moje spravované doméně pomocí vzdálené plochy?
Ne. Nemáte oprávnění pro připojení k řadičům domény k spravované doméně přes vzdálenou plochu. Členové skupiny 'Administrators AAD řadič domény, mohou spravovat spravované doméně pomocí nástroje pro správu AD například Centra správy Active Directory (ADAC) nebo AD PowerShell. Tyto nástroje se instalují pomocí funkce 'nástroje pro správu vzdáleného serveru, v systému Windows server připojený k spravované doméně.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Povolení I Azure AD Domain Services. Jaké uživatelský účet se používá k počítačům připojení k doméně k této doméně?
Členové skupiny pro správu 'správci AAD řadič domény, můžete počítače připojení k doméně. Kromě toho členy této skupiny mají přístup ke vzdálené ploše počítače, které byly připojené k doméně.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Je nutné oprávnění správce domény pro spravovanou doménu poskytované Azure AD Domain Services?
Ne. Nejsou udělena administrativní oprávnění na spravované domény. Oprávnění správce domény a správce podnikové sítě nejsou k dispozici pro použití v rámci domény. Správce stávající domény nebo skupiny správce rozlehlé sítě v rámci adresáře služby Azure AD také nejsou udělena oprávnění správce domény nebo enterprise v doméně.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Můžete upravit členství ve skupinách pomocí protokolu LDAP nebo jiné nástroje pro správu služby AD na spravované domény?
Ne. V doménách, které jsou obsluhovány pomocí Azure AD Domain Services nelze upravit členství ve skupinách. Totéž platí i pro atributy uživatele. Ale může změnit členství ve skupině nebo uživatelské atributy buď ve službě Azure AD, nebo v místní doméně. Tyto změny jsou automaticky synchronizovány do Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak dlouho bude trvat, změny I provést Moje adresář Azure AD mají být zobrazeny v mé spravované domény?
Změny provedené v adresáři služby Azure AD pomocí Azure AD uživatelského rozhraní nebo Powershellu jsou synchronizovány do vaší spravované domény. Tento proces synchronizace běží na pozadí. Po dokončení jednorázové počáteční synchronizace adresáře se obvykle trvá přibližně 20 minut, než změny provedené ve službě Azure AD se projeví ve vaší spravované domény.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Můžete rozšířit schéma spravované domény poskytované Azure AD Domain Services?
Ne. Schéma je spravovaný společností Microsoft pro spravovanou doménu. Rozšíření schématu nejsou podporovány službou Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Můžete změnit nebo přidat záznamy DNS v mé spravované domény?
Ano. Členy skupiny "Správci AAD řadič domény, jsou udělena oprávnění Správce DNS, k úpravě záznamů DNS v spravované domény. Konzolu Správce DNS na počítači se systémem Windows Server připojený k spravované doméně, se můžete použít ke správě DNS. Chcete-li použít konzolu Správce DNS, nainstalujte 'Nástroje serveru DNS', která je součástí volitelné funkce, nástroje pro správu vzdáleného serveru, na serveru. Další informace o [nástroje pro správu, monitorování a řešení potíží s DNS](https://technet.microsoft.com/library/cc753579.aspx) je k dispozici na webu TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Co je doba platnosti zásady hesel ve spravované doméně?
Výchozí doba života heslo na Azure AD Domain Services spravované domény je 90 dnů. Tato doba platnosti hesla není synchronizován s životnost heslo nakonfigurovat ve službě Azure AD. Proto může dojít k situaci, kdy vyprší platnost ve vaší spravované domény hesla uživatelů, ale musí být stále platné ve službě Azure AD. V takových scénářů uživatelé musí změnit své heslo ve službě Azure AD a nové heslo bude synchronizovat s vaší spravované domény. Kromě toho '-nepodporuje není – konec platnosti hesla' a 'user-must-change-password-at-next-logon' atributy pro uživatelské účty nejsou synchronizovány do vaší spravované domény.

## <a name="billing-and-availability"></a>Fakturace a dostupnost
### <a name="is-azure-ad-domain-services-a-paid-service"></a>Je, že služba Azure AD Domain Services placené služby?
Ano. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Je k dispozici bezplatná zkušební verze pro službu?
Tato služba je součástí bezplatné zkušební verze pro Azure. Můžete si zaregistrovat [bezplatnou zkušební verzi jeden měsíc Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Můžete pozastavit spravované doméně služby Azure AD Domain Services? 
Ne. Jakmile povolíte spravované doméně služby Azure AD Domain Services, služba je k dispozici v rámci vámi zvolené virtuální síti, dokud můžete zakázat nebo odstranění spravované doméně. Neexistuje žádný způsob, jak pozastavit službu. Fakturace pokračuje hodinu, dokud neodstraníte spravované domény.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Můžete získat Azure AD Domain Services v rámci Enterprise Mobility Suite (EMS)? Potřebuji Azure AD Premium k použití služby Azure AD Domain Services?
Ne. Služba Azure AD Domain Services je průběžnými platbami služby Azure a není součástí EMS. Azure AD Domain Services lze ve všech edicích služby Azure AD (volné, základní a, Premium). Fakturuje se na hodinu, v závislosti na využití.

### <a name="what-azure-regions-is-the-service-available-in"></a>Jaké oblasti je služba k dispozici v?
Odkazovat [služby Azure podle oblasti](https://azure.microsoft.com/regions/#services/) stránky zobrazíte seznam oblastí Azure, kde je k dispozici služba Azure AD Domain Services.
