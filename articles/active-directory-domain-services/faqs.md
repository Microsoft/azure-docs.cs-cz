---
title: Nejčastější dotazy týkající se Azure AD Domain Services | Microsoft Docs
description: Přečtěte si některé nejčastější dotazy týkající se konfigurace, správy a dostupnosti pro Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/30/2020
ms.author: justinha
ms.openlocfilehash: 89671d0e69d4e526e30c80619b57d698d5a5acc5
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491161"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Nejčastější dotazy týkající se služby Azure Active Directory (Active Directory Domain Services)

Tato stránka obsahuje odpovědi na nejčastější dotazy týkající se Azure Active Directory Domain Services.

## <a name="configuration"></a>Konfigurace

* [Můžu vytvořit více spravovaných domén pro jeden adresář služby Azure AD?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Můžu povolit Azure AD Domain Services v klasické virtuální síti?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Můžu Azure AD Domain Services ve virtuální síti Azure Resource Manager povolit?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Můžu migrovat existující spravovanou doménu z klasické virtuální sítě do Správce prostředků virtuální sítě?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Můžu Azure AD Domain Services povolit v předplatném Azure CSP (Cloud Solution Provider)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Můžu Azure AD Domain Services povolit ve federovaném adresáři služby Azure AD? Nesynchronizuji hodnoty hash hesel do služby Azure AD. Můžu povolit Azure AD Domain Services pro tento adresář?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Můžu Azure AD Domain Services zpřístupnit ve více virtuálních sítích v rámci svého předplatného?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Můžu povolit Azure AD Domain Services používání PowerShellu?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Můžu Azure AD Domain Services povolit použití šablony Správce prostředků?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Můžu přidat řadiče domény do spravované domény Azure AD Domain Services?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Můžou být uživatelé typu Host pozváni do adresáře Azure AD Domain Services použít?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Můžu existující Azure AD Domain Services spravovanou doménu přesunout do jiného předplatného, skupiny prostředků, oblasti nebo virtuální sítě?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Zahrnuje Azure AD Domain Services možnosti vysoké dostupnosti?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Můžu vytvořit více spravovaných domén pro jeden adresář služby Azure AD?
No. Pro jeden adresář služby Azure AD můžete vytvořit jenom jednu spravovanou doménu Azure AD Domain Services.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Můžu povolit Azure AD Domain Services v klasické virtuální síti?
Klasické virtuální sítě nejsou pro nová nasazení podporovaná. Existující spravované domény nasazené v klasických virtuálních sítích se i nadále podporují, dokud nebudou vyřazení z 1. března 2023. U existujících nasazení můžete [migrovat Azure AD Domain Services z modelu klasických virtuálních sítí do Správce prostředků](migrate-from-classic-vnet.md).

Další informace najdete v [oficiálním oznámení o zastarání](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Můžu Azure AD Domain Services ve virtuální síti Azure Resource Manager povolit?
Ano. Azure AD Domain Services lze povolit ve virtuální síti Azure Resource Manager. Při vytváření spravované domény už nejsou dostupné klasické virtuální sítě Azure.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Můžu migrovat existující spravovanou doménu z klasické virtuální sítě do Správce prostředků virtuální sítě?
Ano. Další informace najdete v tématu [migrace Azure AD Domain Services z modelu klasických virtuálních sítí do Správce prostředků](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Můžu Azure AD Domain Services povolit v předplatném Azure CSP (Cloud Solution Provider)?
Ano. Další informace najdete v tématu [povolení Azure AD Domain Services v předplatných služby Azure CSP](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Můžu Azure AD Domain Services povolit ve federovaném adresáři služby Azure AD? Nesynchronizuji hodnoty hash hesel do služby Azure AD. Můžu povolit Azure AD Domain Services pro tento adresář?
No. K ověřování uživatelů pomocí protokolu NTLM nebo Kerberos Azure AD Domain Services potřebuje přístup k hodnotám hash hesel uživatelských účtů. Ve federovaném adresáři nejsou hodnoty hash hesel uložené v adresáři služby Azure AD. Proto Azure AD Domain Services nefunguje s těmito adresáři služby Azure AD.

Pokud však používáte Azure AD Connect pro synchronizaci hodnot hash hesel, můžete použít Azure AD Domain Services, protože hodnoty hash hesla jsou uloženy v Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Můžu Azure AD Domain Services zpřístupnit ve více virtuálních sítích v rámci svého předplatného?
Samotná služba přímo nepodporuje tento scénář. Vaše spravovaná doména je dostupná v jednom okamžiku jenom v jedné virtuální síti. Můžete ale nakonfigurovat připojení mezi několika virtuálními sítěmi a zpřístupnit Azure AD Domain Services jiným virtuálním sítím. Další informace najdete v tématu [Postup připojení virtuálních sítí v Azure pomocí bran VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) nebo [partnerských vztahů virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Můžu povolit Azure AD Domain Services používání PowerShellu?
Ano. Další informace najdete v tématu [povolení Azure AD Domain Services pomocí prostředí PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Můžu Azure AD Domain Services povolit použití šablony Správce prostředků?
Ano, Azure AD Domain Services spravovanou doménu můžete vytvořit pomocí Správce prostředků šablony. Před nasazením šablony musí být vytvořený instanční objekt a skupina služby Azure AD pro správu pomocí Azure Portal nebo Azure PowerShell. Další informace najdete v tématu [Vytvoření spravované domény Azure služba AD DS pomocí šablony Azure Resource Manager](template-create-instance.md). Když vytvoříte Azure AD Domain Services spravovanou doménu v Azure Portal, je k dispozici také možnost Exportovat šablonu pro použití s dalšími nasazeními.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Můžu přidat řadiče domény do spravované domény Azure AD Domain Services?
No. Doména, kterou poskytuje Azure AD Domain Services, je spravovaná doména. Pro tuto doménu nemusíte zřizovat, konfigurovat ani jinak spravovat řadiče domény. Tyto aktivity správy jsou poskytovány jako služba společnosti Microsoft. Proto pro spravovanou doménu nemůžete přidat další řadiče domény (pro čtení i zápis nebo jen pro čtení).

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Můžou být uživatelé typu Host pozváni do adresáře Azure AD Domain Services použít?
No. Uživatelé typu Host, kteří se pozvali k adresáři Azure AD pomocí procesu pozvání [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) , se synchronizují do vaší Azure AD Domain Services spravované domény. Hesla pro tyto uživatele ale nejsou uložená ve vašem adresáři služby Azure AD. Proto Azure AD Domain Services nemá žádný způsob, jak pro tyto uživatele synchronizovat hodnoty hash protokolu NTLM a Kerberos do spravované domény. Tito uživatelé se nemohou přihlašovat nebo připojovat k počítačům spravované doméně.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Můžu existující Azure AD Domain Services spravovanou doménu přesunout do jiného předplatného, skupiny prostředků, oblasti nebo virtuální sítě?
No. Po vytvoření spravované domény Azure AD Domain Services nemůžete tuto spravovanou doménu přesunout do jiné skupiny prostředků, virtuální sítě, předplatného atd. Při nasazení spravované domény se ujistěte, že vyberete nejvhodnější předplatné, skupinu prostředků, oblast a virtuální síť.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Zahrnuje Azure AD Domain Services možnosti vysoké dostupnosti?

Ano. Každá Azure AD Domain Services spravovaná doména obsahuje dva řadiče domény. Tyto řadiče domény nespravujete ani se k nim nepřipojujete, jsou součástí spravované služby. Pokud nasadíte Azure AD Domain Services do oblasti, která podporuje Zóny dostupnosti, řadiče domény se rozdělují mezi zóny. V oblastech, které nepodporují Zóny dostupnosti, jsou řadiče domény distribuované napříč skupinami dostupnosti. Pro tuto distribuci nemáte žádné možnosti konfigurace nebo řízení správy. Další informace najdete v tématu [Možnosti dostupnosti pro virtuální počítače v Azure](../virtual-machines/availability.md).

## <a name="administration-and-operations"></a>Správa a operace

* [Můžu se k řadiči domény pro spravovanou doménu připojit pomocí vzdálené plochy?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Povolil (a) Azure AD Domain Services. Jaký uživatelský účet mám použít k připojení počítačů do domény k této doméně?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Mám oprávnění správce domény pro spravovanou doménu, kterou poskytuje Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Je možné upravit členství ve skupinách pomocí protokolu LDAP nebo jiných nástrojů pro správu služby AD ve spravovaných doménách?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Jak dlouho trvá, aby se změny v adresáři služby Azure AD zobrazily ve spravované doméně?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Můžu roztáhnout schéma spravované domény, které poskytuje Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Můžu v mé spravované doméně upravovat nebo přidávat záznamy DNS?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Jaké jsou zásady životního cyklu hesel ve spravované doméně?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Poskytuje Azure AD Domain Services ochranu před uzamčením účtu AD?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Je možné nakonfigurovat systém souborů DFS (Distributed File System) (DFS) a replikaci v rámci Azure AD Domain Services?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Jak se používají aktualizace Windows v Azure AD Domain Services?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Můžu se k řadiči domény pro spravovanou doménu připojit pomocí vzdálené plochy?
No. Nemáte oprávnění pro připojení k řadičům domény pro spravovanou doménu pomocí vzdálené plochy. Členové skupiny *Správci AAD DC* můžou spravovat spravovanou doménu pomocí nástrojů pro správu služby AD, jako je centrum pro správu služby Active Directory (ADAC) nebo prostředí AD PowerShell. Tyto nástroje se instalují pomocí funkce *Nástroje pro vzdálenou správu serveru* na Windows serveru připojeném ke spravované doméně. Další informace najdete v tématu [Vytvoření virtuálního počítače pro správu pro konfiguraci a správu Azure AD Domain Services spravované domény](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Povolil (a) Azure AD Domain Services. Jaký uživatelský účet mám použít k připojení počítačů do domény k této doméně?
Každý uživatelský účet, který je součástí spravované domény, se může připojit k virtuálnímu počítači. Členové skupiny *Správci AAD DC* mají oprávnění ke vzdálené ploše pro počítače, které jsou připojené ke spravované doméně.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Mám oprávnění správce domény pro spravovanou doménu, kterou poskytuje Azure AD Domain Services?
No. Ve spravované doméně neudělíte oprávnění správce. Oprávnění *správce domény* a *podnikového správce* nejsou k dispozici pro použití v rámci domény. Členům skupiny Domain Administrators nebo Enterprise Administrators ve vaší místní službě Active Directory se taky ve spravované doméně neudělují oprávnění správce domény nebo rozlehlé sítě.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Je možné upravit členství ve skupinách pomocí protokolu LDAP nebo jiných nástrojů pro správu služby AD ve spravovaných doménách?
Uživatele a skupiny, které jsou synchronizované z Azure Active Directory Azure AD Domain Services nelze upravovat, protože jejich zdroj původu je Azure Active Directory. To zahrnuje přesun uživatelů nebo skupin z organizační jednotky spravované AADDC uživateli do vlastní organizační jednotky. Je možné upravit všechny uživatele nebo skupiny pocházející ze spravované domény.  

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Jak dlouho trvá, aby se změny v adresáři služby Azure AD zobrazily ve spravované doméně?
Změny provedené v adresáři Azure AD pomocí uživatelského rozhraní služby Azure AD nebo PowerShellu se automaticky synchronizují do vaší spravované domény. Tento proces synchronizace běží na pozadí. Pro tuto synchronizaci není definováno žádné časové období, aby se dokončily všechny změny objektu.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Můžu roztáhnout schéma spravované domény, které poskytuje Azure AD Domain Services?
No. Schéma je spravováno společností Microsoft pro spravovanou doménu. Azure AD Domain Services nepodporuje rozšíření schématu.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Můžu v mé spravované doméně upravovat nebo přidávat záznamy DNS?
Ano. Členům skupiny *AAD DC Administrators* jsou udělena oprávnění *Správce DNS* pro úpravu záznamů DNS ve spravované doméně. Uživatelé mohou pomocí konzoly Správce DNS na počítači se systémem Windows Server, který je připojen ke spravované doméně, spravovat službu DNS. Pokud chcete použít konzolu Správce DNS, nainstalujte *Nástroje serveru DNS*, které jsou součástí *Nástroje pro vzdálenou správu serveru* volitelné funkce na serveru. Další informace najdete v tématu [Správa DNS ve spravované doméně Azure AD Domain Services](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Jaké jsou zásady životního cyklu hesel ve spravované doméně?
Výchozí doba života hesla ve spravované doméně Azure AD Domain Services je 90 dní. Doba života hesla není synchronizovaná s životností hesla nakonfigurovanou v Azure AD. Proto můžete mít situaci, kdy vyprší platnost hesel uživatelů ve vaší spravované doméně, ale ve službě Azure AD stále platí. V takových scénářích potřebují uživatelé změnit heslo v Azure AD a nové heslo se synchronizuje do vaší spravované domény. Pokud chcete změnit výchozí dobu života hesla ve spravované doméně, můžete [vytvořit a nakonfigurovat vlastní zásady hesel.](password-policy.md)

Kromě toho se zásady hesel Azure AD pro *DisablePasswordExpiration* synchronizovaly do spravované domény. Při použití *DisablePasswordExpiration* pro uživatele v Azure AD má *DONT_EXPIRE_PASSWORD* *použita hodnota pro* synchronizovaného uživatele ve spravované doméně.

Když uživatelé resetují heslo ve službě Azure AD, použije se atribut *forceChangePasswordNextSignIn = true* . Spravovaná doména synchronizuje tento atribut z Azure AD. Když spravovaná doména zjistí, že *forceChangePasswordNextSignIn* je nastavená pro synchronizovaného uživatele z Azure AD, je atribut *pwdLastSet* ve spravované doméně nastavený na *0*, který zruší platnost aktuálně nastaveného hesla.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Poskytuje Azure AD Domain Services ochranu před uzamčením účtu AD?
Ano. Pět neplatných pokusů o zadání hesla ve spravované doméně způsobí, že se uživatelský účet zamkne po dobu 30 minut. Po 30 minutách se uživatelský účet automaticky odemkne. Neplatné pokusy o zadání hesla ve spravované doméně nezamkne uživatelský účet ve službě Azure AD. Uživatelský účet je uzamčený pouze v rámci vaší Azure AD Domain Services spravované domény. Další informace najdete v tématu [zásady hesel a uzamčení účtů ve spravovaných doménách](password-policy.md).

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Je možné nakonfigurovat systém souborů DFS (Distributed File System) a replikaci v rámci Azure AD Domain Services?
No. Systém souborů DFS (Distributed File System) (DFS) a replikace nejsou k dispozici při použití Azure AD Domain Services.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Jak se používají aktualizace Windows v Azure AD Domain Services?
Řadiče domény ve spravované doméně automaticky použijí požadované aktualizace systému Windows. Tady se nedá nic konfigurovat ani spravovat. Ujistěte se, že nevytváříte pravidla skupiny zabezpečení sítě, která blokují odchozí provoz do aktualizací Windows. Pro vlastní virtuální počítače připojené ke spravované doméně zodpovídáte za konfiguraci a používání všech požadovaných aktualizací operačního systému a aplikací.

## <a name="billing-and-availability"></a>Fakturace a dostupnost

* [Je Azure AD Domain Services placená služba?](#is-azure-ad-domain-services-a-paid-service)
* [Máte k dispozici bezplatnou zkušební verzi služby?](#is-there-a-free-trial-for-the-service)
* [Můžu pozastavit Azure AD Domain Services spravovanou doménu?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Můžu převzít služby při selhání Azure AD Domain Services do jiné oblasti pro událost zotavení po havárii?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Můžu Azure AD Domain Services jako součást sady Enterprise Mobility Suite (EMS)? Potřebuji Azure AD Premium používat Azure AD Domain Services?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [K jakým oblastem Azure je služba dostupná?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Je Azure AD Domain Services placená služba?
Ano. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Máte k dispozici bezplatnou zkušební verzi služby?
Azure AD Domain Services je součástí bezplatné zkušební verze pro Azure. Můžete si zaregistrovat [bezplatnou měsíční zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Můžu pozastavit Azure AD Domain Services spravovanou doménu?
No. Jakmile povolíte Azure AD Domain Services spravovanou doménu, služba bude dostupná v rámci vybrané virtuální sítě, dokud neodstraníte spravovanou doménu. Neexistuje žádný způsob, jak službu pozastavit. Fakturace bude pokračovat po hodinách, dokud neodstraníte spravovanou doménu.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Můžu převzít služby při selhání Azure AD Domain Services do jiné oblasti pro událost zotavení po havárii?
No. Azure AD Domain Services aktuálně neposkytuje geograficky redundantní model nasazení. Je omezená na jednu virtuální síť v oblasti Azure. Pokud chcete využívat více oblastí Azure, musíte spustit řadiče Doména služby Active Directory na virtuálních počítačích Azure s IaaS. Pokyny k architektuře najdete v tématu věnovaném [rozšiřování místní domény služby Active Directory do Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Můžu Azure AD Domain Services jako součást sady Enterprise Mobility Suite (EMS)? Potřebuji Azure AD Premium používat Azure AD Domain Services?
No. Azure AD Domain Services je služba Azure s průběžnými platbami, která není součástí EMS. Azure AD Domain Services lze použít se všemi edicemi služby Azure AD (Free a Premium). Fakturuje se po hodinách podle využití.

### <a name="what-azure-regions-is-the-service-available-in"></a>K jakým oblastem Azure je služba dostupná?
Seznam oblastí Azure, kde je Azure AD Domain Services k dispozici, najdete na stránce [služby Azure podle oblasti](https://azure.microsoft.com/regions/#services/) .

## <a name="troubleshooting"></a>Řešení potíží

Řešení běžných potíží s konfigurací nebo správou Azure AD Domain Services najdete v [Průvodci odstraňováním potíží](troubleshoot.md) .

## <a name="next-steps"></a>Další kroky

Další informace o Azure AD Domain Services najdete v tématu [co je Azure Active Directory Domain Services?](overview.md).

Informace o tom, jak začít, najdete v tématu [Vytvoření a konfigurace Azure Active Directory Domain Services spravované domény](tutorial-create-instance.md).
