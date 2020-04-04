---
title: Nejčastější dotazy týkající se služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Přečtěte si a porozumíte některým nejčastějším dotazem týkajícím se konfigurace, správy a dostupnosti pro službu Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
ms.openlocfilehash: 86b68b794928900717bea25623e7eb833c23e86c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655354"
---
# <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy (ČASTÉ OTÁZKY)

Tato stránka odpovídá na nejčastější dotazy týkající se služby Azure Active Directory Domain Services.

## <a name="configuration"></a>Konfigurace

* [Můžu vytvořit víc spravovaných domén pro jeden adresář Azure AD?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Můžu povolit služby Azure AD Domain Services v klasické virtuální síti?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Můžu povolit služby Azure AD Domain Services ve virtuální síti Azure Resource Manager?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Můžu migrovat svou stávající spravovanou doménu z klasické virtuální sítě do virtuální sítě Resource Manageru?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Můžu povolit služby Azure AD Domain Services v předplatném Azure CSP (Cloud Solution Provider)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Můžu povolit služby Azure AD Domain Services ve federovaném adresáři Azure AD? Nesynchronizuji hashe hesla do služby Azure AD. Můžu pro tento adresář povolit službu Azure AD Domain Services?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Můžu v rámci předplatného zpřístupnit služby Azure AD Domain Services ve více virtuálních sítích?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Můžu povolit služby Azure AD Domain Services pomocí PowerShellu?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Můžu povolit služby Azure AD Domain Services pomocí šablony Správce prostředků?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Můžu do spravované domény služby Azure AD Domain Services přidat řadiče domény?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Můžou uživatelé typu Host pozvaní do mého adresáře používat službu Azure AD Domain Services?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Můžu přesunout existující spravovanou doménu služby Azure AD Domain Services do jiného předplatného, skupiny prostředků, oblasti nebo virtuální sítě?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Obsahuje služby Azure AD Domain Services možnosti vysoké dostupnosti?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Můžu vytvořit víc spravovaných domén pro jeden adresář Azure AD?
Ne. Můžete vytvořit jenom jednu spravovanou doménu obsluhovanou službou Azure AD Domain Services pro jeden adresář Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Můžu povolit služby Azure AD Domain Services v klasické virtuální síti?
Klasické virtuální sítě nejsou podporovány pro nová nasazení. Existující spravované domény nasazené ve virtuálních sítích Classic jsou nadále podporovány, dokud nebudou vyřazeny z 1. U existujících nasazení můžete [migrovat služby Azure AD Domain Services z modelu klasické virtuální sítě do Správce prostředků](migrate-from-classic-vnet.md).

Další informace naleznete v [oficiálním oznámení o vyřazení](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Můžu povolit služby Azure AD Domain Services ve virtuální síti Azure Resource Manager?
Ano. Služby Azure AD Domain Services můžou být povolené ve virtuální síti Azure Resource Manageru. Klasické virtuální sítě Azure už nejsou dostupné při vytváření spravované domény.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Můžu migrovat svou stávající spravovanou doménu z klasické virtuální sítě do virtuální sítě Resource Manageru?
Ano. Další informace najdete [v tématu Migrace služby Domény Azure AD z modelu klasické virtuální sítě do Správce prostředků](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Můžu povolit služby Azure AD Domain Services v předplatném Azure CSP (Cloud Solution Provider)?
Ano. Další informace najdete v tématu [povolení služby Azure AD Domain Services v předplatných Azure CSP](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Můžu povolit služby Azure AD Domain Services ve federovaném adresáři Azure AD? Nesynchronizuji hashe hesla do služby Azure AD. Můžu pro tento adresář povolit službu Azure AD Domain Services?
Ne. K ověření uživatelů prostřednictvím NTLM nebo Kerberos potřebuje služba Azure AD Domain Services přístup k hodnotám hashe hesel uživatelských účtů. Ve federovaném adresáři nejsou v adresáři Azure AD uloženy hashe hesel. Proto služby Azure AD Domain Services nefunguje s takovými adresáři Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Můžu v rámci předplatného zpřístupnit služby Azure AD Domain Services ve více virtuálních sítích?
Služba sama o sobě přímo nepodporuje tento scénář. Vaše spravovaná doména je najednou dostupná jenom v jedné virtuální síti. Můžete však nakonfigurovat připojení mezi více virtuálními sítěmi tak, aby byly služby Azure AD Domain Services vystaveny jiným virtuálním sítím. Další informace najdete v tématu [jak připojit virtuální sítě v Azure pomocí bran VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) nebo [partnerského vztahu virtuální sítě](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Můžu povolit služby Azure AD Domain Services pomocí PowerShellu?
Ano. Další informace najdete v tématu [povolení služby Azure AD Domain Services pomocí Prostředí PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Můžu povolit služby Azure AD Domain Services pomocí šablony Správce prostředků?
Ano, spravovanou doménu služby Azure AD Domain Services můžete vytvořit pomocí šablony Správce prostředků. Instanční objekt a skupina Azure AD pro správu musí být vytvořeny pomocí portálu Azure nebo Azure PowerShellpřed nasazením šablony. Další informace najdete [v tématu Vytvoření spravované domény Azure AD DS pomocí šablony Azure Resource Manager](template-create-instance.md). Když vytvoříte spravovanou doménu služby Azure AD Domain Services na webu Azure Portal, je tu také možnost exportovat šablonu pro použití s dalšími nasazeními.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Můžu do spravované domény služby Azure AD Domain Services přidat řadiče domény?
Ne. Doména poskytovaná službou Azure AD Domain Services je spravovaná doména. Pro tuto doménu není nutné zřčovat, konfigurovat ani jinak spravovat řadiče domény. Tyto aktivity správy jsou poskytovány jako služba společností Microsoft. Proto nelze přidat další řadiče domény (jen pro čtení a zápis nebo jen pro čtení) pro spravovanou doménu.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Můžou uživatelé typu Host pozvaní do mého adresáře používat službu Azure AD Domain Services?
Ne. Uživatelé typu Host pozvaní do adresáře Azure AD pomocí procesu pozvání [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) se synchronizují do spravované domény služby Azure AD Domain Services. Hesla pro tyto uživatele se však neukládají do adresáře Azure AD. Proto Azure AD Domain Services nemá žádný způsob, jak synchronizovat ntlm a kerberos hash pro tyto uživatele do spravované domény. Tito uživatelé se nemohou přihlásit nebo připojit počítače ke spravované doméně.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Můžu přesunout existující spravovanou doménu služby Azure AD Domain Services do jiného předplatného, skupiny prostředků, oblasti nebo virtuální sítě?
Ne. Po vytvoření spravované domény služby Azure AD Domain Services nelze potom přesunout instanci do jiné skupiny prostředků, virtuální sítě, předplatného atd. Při nasazení instance Azure AD DS vyberte nejvhodnější předplatné, skupinu prostředků, oblast a virtuální síť.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Obsahuje služby Azure AD Domain Services možnosti vysoké dostupnosti?

Ano. Každá spravovaná doména služby Azure AD Domain Services obsahuje dva řadiče domény. Nespravujete tyto řadiče domény ani se k němu nepřipojujete, jsou součástí spravované služby. Pokud nasadíte služby Azure AD Domain Services do oblasti, která podporuje zóny dostupnosti, řadiče domény jsou distribuovány mezi zónami. V oblastech, které nepodporují zóny dostupnosti, jsou řadiče domény distribuovány mezi sadami dostupnosti. Nemáte žádné možnosti konfigurace nebo řízení nad touto distribucí. Další informace najdete v [tématu Možnosti dostupnosti pro virtuální počítače v Azure](../virtual-machines/windows/availability.md).

## <a name="administration-and-operations"></a>Správa a provoz

* [Mohu se připojit k řadiči domény pro spravovanou doménu pomocí vzdálené plochy?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Povolil(a) jsem službu Azure AD Domain Services. Jaký uživatelský účet mám použít k doméně připojit počítače k této doméně?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Mám oprávnění správce domény pro spravovanou doménu poskytovanou službou Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Mohu upravit členství ve skupinách pomocí protokolu LDAP nebo jiných nástrojů pro správu služby AD ve spravovaných doménách?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Jak dlouho trvá, než se změny provedené v adresáři Azure AD zobrazí ve spravované doméně?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Můžu rozšířit schéma spravované domény poskytované službou Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Můžu ve spravované doméně upravovat nebo přidávat záznamy DNS?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Jaké jsou zásady životnosti hesla ve spravované doméně?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Poskytuje služby Azure AD Domain Services ochranu proti uzamčení účtu služby AD?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Mohu se připojit k řadiči domény pro spravovanou doménu pomocí vzdálené plochy?
Ne. Nemáte oprávnění k připojení k řadičům domény pro spravovanou doménu pomocí vzdálené plochy. Členové *skupiny AAD DC Administrators* mohou spravovat spravovanou doménu pomocí nástrojů pro správu služby AD, jako je Například Centrum pro správu služby Active Directory (ADAC) nebo Prostředí AD PowerShell. Tyto nástroje jsou nainstalovány pomocí funkce *Nástroje pro vzdálenou správu serveru* na serveru windows, který je připojen ke spravované doméně. Další informace najdete [v tématu Vytvoření virtuálního počítače pro správu pro konfiguraci a správu spravované domény služby Azure AD Domain Services](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Povolil(a) jsem službu Azure AD Domain Services. Jaký uživatelský účet mám použít k doméně připojit počítače k této doméně?
K virtuálnímu počítači se může připojit libovolný uživatelský účet, který je součástí spravované domény Azure AD DS. Členům *skupiny AAD DC Administrators* je udělen přístup ke vzdálené ploše počítačům, které byly připojeny ke spravované doméně.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Mám oprávnění správce domény pro spravovanou doménu poskytovanou službou Azure AD Domain Services?
Ne. Ve spravované doméně nejsou udělena oprávnění správce. *Oprávnění Správce domény* a *Správce rozlehlé sítě* nejsou k dispozici pro použití v doméně. Členům skupin správce domény nebo podnikových správců ve vaší místní službě Active Directory také nejsou udělena oprávnění domény nebo správce rozlehlé sítě ve spravované doméně.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Mohu upravit členství ve skupinách pomocí protokolu LDAP nebo jiných nástrojů pro správu služby AD ve spravovaných doménách?
Uživatele a skupiny synchronizované ze služby Azure Active Directory do služby Azure AD Domain Services nelze změnit, protože jejich zdrojem původu je služba Azure Active Directory. Může být změněn libovolný uživatel nebo skupina pocházející ze spravované domény.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak dlouho trvá, než se změny provedené v adresáři Azure AD zobrazí ve spravované doméně?
Změny provedené v adresáři Azure AD pomocí azure ad ui nebo PowerShellu se automaticky synchronizují do vaší spravované domény. Tento proces synchronizace běží na pozadí. Neexistuje žádné definované časové období pro tuto synchronizaci k dokončení všech změn objektu.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Můžu rozšířit schéma spravované domény poskytované službou Azure AD Domain Services?
Ne. Schéma je spravováno společností Microsoft pro spravovanou doménu. Rozšíření schématu nejsou podporovány službou Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Můžu ve spravované doméně upravovat nebo přidávat záznamy DNS?
Ano. Členům *skupiny AAD DC Administrators* jsou udělena oprávnění *správce DNS* k úpravám záznamů DNS ve spravované doméně. Tito uživatelé mohou ke správě služby DNS používat konzolu SPRÁVCE DNS v počítači se systémem Windows Server, který je připojen ke spravované doméně. Chcete-li používat konzolu Správce DNS, nainstalujte *nástroje serveru DNS*, které jsou součástí volitelné funkce Nástroje pro *vzdálenou správu serveru* na serveru. Další informace najdete [v tématu Správa DNS ve spravované doméně služby Azure AD Domain Services](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Jaké jsou zásady životnosti hesla ve spravované doméně?
Výchozí doba platnosti hesla ve spravované doméně služby Azure AD Domain Services je 90 dní. Tato životnost hesla není synchronizována s životnosthesla nakonfigurovanou ve službě Azure AD. Proto může dojít k situaci, kdy hesla uživatelů vyprší ve spravované doméně, ale jsou stále platné ve službě Azure AD. V takových scénářích uživatelé potřebují změnit své heslo ve službě Azure AD a nové heslo se synchronizuje do vaší spravované domény. Kromě toho hesla *nevyprší* a *uživatel-must-change-password-at-next-logon* atributy pro uživatelské účty nejsou synchronizovány do spravované domény.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Poskytuje služby Azure AD Domain Services ochranu proti uzamčení účtu služby AD?
Ano. Pět neplatných pokusů o heslo během 2 minut ve spravované doméně způsobí uzamčení uživatelského účtu po dobu 30 minut. Po 30 minutách se uživatelský účet automaticky odemkne. Neplatné pokusy o heslo ve spravované doméně nezamykají uživatelský účet ve službě Azure AD. Uživatelský účet je uzamčen pouze v rámci spravované domény služby Azure AD Domain Services. Další informace naleznete v [tématu Zásady uzamčení hesla a účtu ve spravovaných doménách](password-policy.md).

## <a name="billing-and-availability"></a>Fakturace a dostupnost

* [Je služba Azure AD Domain Services placenou službou?](#is-azure-ad-domain-services-a-paid-service)
* [Existuje bezplatná zkušební verze pro službu?](#is-there-a-free-trial-for-the-service)
* [Můžu pozastavit spravovanou doménu služby Azure AD Domain Services?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Můžu pro událost zotavení po havárii převést na služby Azure AD Domain Services do jiné oblasti?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Můžu získat služby Azure AD Domain Services jako součást sady Enterprise Mobility Suite (EMS)? Potřebuji Azure AD Premium k používání služby Azure AD Domain Services?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [V jakých oblastech Azure je služba dostupná?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Je služba Azure AD Domain Services placenou službou?
Ano. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Existuje bezplatná zkušební verze pro službu?
Služby Azure AD Domain Services jsou součástí bezplatné zkušební verze pro Azure. Můžete si zaregistrovat [bezplatnou měsíční zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Můžu pozastavit spravovanou doménu služby Azure AD Domain Services?
Ne. Jakmile poaktivujete spravovanou doménu služby Azure AD Domain Services, bude služba dostupná ve vybrané virtuální síti, dokud spravovanou doménu neodstraníte. Neexistuje žádný způsob, jak pozastavit službu. Fakturace pokračuje každou hodinu, dokud neodstraníte spravovanou doménu.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Můžu pro událost zotavení po havárii převést na služby Azure AD Domain Services do jiné oblasti?
Ne. Služba Azure AD Domain Services aktuálně neposkytuje geograficky redundantní model nasazení. Je omezená na jednu virtuální síť v oblasti Azure. Pokud chcete využívat více oblastí Azure, musíte spustit řadiče domény služby Active Directory na virtuálních počítačích Azure IaaS. Pokyny k architektuře najdete [v tématu Rozšíření místní domény Služby Active Directory do Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Můžu získat služby Azure AD Domain Services jako součást sady Enterprise Mobility Suite (EMS)? Potřebuji Azure AD Premium k používání služby Azure AD Domain Services?
Ne. Azure AD Domain Services je služba Azure s průběžným platbou za chvíli a není součástí EMS. Azure AD Domain Services se dá používat se všemi edicemi Azure AD (Free a Premium). V závislosti na využití se účtuje hodinová.

### <a name="what-azure-regions-is-the-service-available-in"></a>V jakých oblastech Azure je služba dostupná?
Seznam oblastí Azure, kde jsou dostupné služby Azure AD Domain Services, najdete na stránce [Služby Azure podle oblastí.](https://azure.microsoft.com/regions/#services/)

## <a name="troubleshooting"></a>Řešení potíží

Řešení běžných problémů s konfigurací nebo správou služby Azure AD Domain Services najdete v [průvodci odstraňováním potíží.](troubleshoot.md)

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure AD Domain Services najdete v tématu [Co je Azure Active Directory Domain Services?](overview.md)

Informace o tom, jak začít, najdete [v tématu Vytvoření a konfigurace instance služby Azure Active Directory Domain Services](tutorial-create-instance.md).
