---
title: Běžné scénáře nasazení pro Azure AD Domain Services | Microsoft Docs
description: Přečtěte si o některých běžných scénářích a případech použití Azure Active Directory Domain Services k poskytnutí hodnoty a splnění obchodních potřeb.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: justinha
ms.openlocfilehash: d33698ed2f9ac53aae3c836acd54f19a4b72ceef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619024"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Běžné případy použití a scénáře pro Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure služba AD DS) poskytují spravované doménové služby, jako je připojení k doméně, zásady skupiny, protokol LDAP (Lightweight Directory Access Protocol) a ověřování pomocí protokolu Kerberos/NTLM. Azure služba AD DS se integruje s vaším stávajícím tenant Azure AD, který umožňuje uživatelům přihlásit se pomocí jejich stávajících přihlašovacích údajů. Tyto doménové služby budete používat bez nutnosti nasazovat, spravovat a opravovat řadiče domény v cloudu, což poskytuje plynulejší a posunutí místních prostředků do Azure.

Tento článek popisuje některé běžné obchodní scénáře, ve kterých Azure služba AD DS poskytuje hodnotu a splňuje tyto potřeby.

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Běžné způsoby poskytování řešení identity v cloudu

Když migrujete stávající úlohy do cloudu, aplikace podporující adresáře můžou používat protokol LDAP pro přístup pro čtení nebo zápis do místního adresáře služba AD DS. Aplikace, které běží na Windows serveru, se obvykle nasazují na virtuální počítače připojené k doméně, aby je bylo možné bezpečně spravovat pomocí Zásady skupiny. K ověřování koncových uživatelů se můžou aplikace spoléhat i na integrované ověřování Windows, jako je například ověřování pomocí protokolu Kerberos nebo NTLM.

Správci IT často používají jedno z následujících řešení k poskytování služby identity aplikacím, které běží v Azure:

* Nakonfigurujte připojení VPN typu Site-to-site mezi úlohami spuštěnými v Azure a místním prostředím služba AD DS.
    * Místní řadiče domény pak poskytují ověření prostřednictvím připojení VPN.
* Pomocí virtuálních počítačů Azure můžete vytvořit repliku řadičů domény, aby se služba AD DS doménová a doménová struktura z místního prostředí rozšířila.
    * Řadiče domény, které běží na virtuálních počítačích Azure, poskytují ověřování a replikují informace o adresáři mezi místním prostředím služba AD DS.
* Nasazení samostatného služba AD DSho prostředí v Azure pomocí řadičů domény, které běží na virtuálních počítačích Azure.
    * Řadiče domény, které běží na virtuálních počítačích Azure, poskytují ověřování, ale žádné informace o adresáři se replikují z místního prostředí služba AD DS.

Díky těmto přístupům sítě VPN k místnímu adresáři připojovat aplikace k přechodným síťovým histogramu nebo výpadkům sítě. Pokud nasadíte řadiče domény pomocí virtuálních počítačů v Azure, IT tým musí spravovat virtuální počítače a pak je zabezpečit, opravovat, monitorovat, zálohovat a řešit.

Azure služba AD DS nabízí alternativy k nutnosti vytvářet připojení VPN zpátky do místního prostředí služba AD DS nebo spouštět a spravovat virtuální počítače v Azure za účelem poskytování služeb identit. Služba Azure služba AD DS jako spravovaná služba zjednodušuje vytváření řešení integrované identity pro hybridní i cloudové prostředí.

> [!div class="nextstepaction"]
> [Porovnání Azure služba AD DS s Azure AD a samoobslužnou správou služba AD DS na virtuálních počítačích Azure nebo v místním prostředí][compare]

## <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure služba AD DS pro hybridní organizace

Řada organizací spouští hybridní infrastrukturu, která zahrnuje úlohy cloudových i místních aplikací. Starší verze aplikací migrované do Azure jako součást strategie výtahu a posunutí můžou k poskytování informací o identitě používat tradiční připojení LDAP. Aby bylo možné podporovat tuto hybridní infrastrukturu, informace o identitě z místního prostředí služba AD DS lze synchronizovat s klientem služby Azure AD. Azure služba AD DS pak poskytuje tyto starší aplikace v Azure se zdrojem identity, aniž by bylo nutné konfigurovat a spravovat připojení aplikací zpátky do místních adresářových služeb.

Pojďme se podívat na příklad pro společnost Litware Corporation, hybridní organizaci, která spouští místní i prostředky Azure:

![Azure Active Directory Domain Services pro hybridní organizaci, která zahrnuje místní synchronizaci](./media/overview/synced-tenant.png)

* Úlohy aplikací a serverů, které vyžadují službu Domain Services, se nasazují ve virtuální síti v Azure.
    * To může zahrnovat starší aplikace migrované do Azure v rámci strategie navýšení a posunutí.
* Pokud chcete synchronizovat informace o identitě z místního adresáře do svého tenanta Azure AD, Litware Corporation nasadí [Azure AD Connect][azure-ad-connect].
    * Mezi informace o identitě, která se synchronizují, patří uživatelské účty a členství ve skupinách.
* Litware IT tým umožňuje služba AD DS Azure AD pro svého tenanta Azure AD v této nebo partnerské virtuální síti.
* Aplikace a virtuální počítače nasazené ve virtuální síti Azure pak můžou používat funkce Azure služba AD DS, jako je připojení k doméně, čtení LDAP, vázání LDAP, ověřování NTLM a Kerberos, a Zásady skupiny.

> [!IMPORTANT]
> Azure AD Connect by měl být nainstalovaný a nakonfigurovaný jenom pro synchronizaci s místními služba AD DS prostředími. Instalace Azure AD Connect ve spravované doméně není podporovaná pro synchronizaci objektů zpět do Azure AD.

## <a name="azure-ad-ds-for-cloud-only-organizations"></a>služba AD DS Azure pro cloudové organizace

Pouze cloudový tenant Azure AD nemá místní zdroj identity. Uživatelské účty a členství ve skupinách jsou například vytvářeny a spravovány přímo ve službě Azure AD.

Teď se podíváme na příklad pro contoso, cloudovou organizaci, která pro identitu používá Azure AD. V Azure AD se vytvářejí a spravují všechny identity uživatelů, jejich přihlašovací údaje a členství ve skupinách. Neexistuje žádná další konfigurace Azure AD Connect k synchronizaci jakýchkoli informací o identitě z místního adresáře.

![Azure Active Directory Domain Services pro cloudovou organizaci bez místní synchronizace](./media/overview/cloud-only-tenant.png)

* Úlohy aplikací a serverů, které vyžadují službu Domain Services, se nasazují ve virtuální síti v Azure.
* Tým IT společnosti Contoso povoluje Azure služba AD DS pro svého tenanta Azure AD v této nebo partnerské virtuální síti.
* Aplikace a virtuální počítače nasazené ve virtuální síti Azure pak můžou používat funkce Azure služba AD DS, jako je připojení k doméně, čtení LDAP, vázání LDAP, ověřování NTLM a Kerberos, a Zásady skupiny.

## <a name="secure-administration-of-azure-virtual-machines"></a>Zabezpečená správa virtuálních počítačů Azure

Pokud chcete použít jednu sadu přihlašovacích údajů služby AD, můžete virtuální počítače Azure připojit k spravované doméně Azure služba AD DS. Tento přístup snižuje problémy se správou přihlašovacích údajů, jako je třeba Údržba účtů místních správců na jednotlivých virtuálních počítačích nebo samostatné účty a hesla mezi prostředími.

Virtuální počítače, které jsou připojené ke spravované doméně, se dají spravovat i zabezpečit pomocí zásad skupiny. Požadované standardní hodnoty zabezpečení je možné použít pro virtuální počítače, které je zablokují v souladu s pokyny pro zabezpečení společnosti. Pomocí možností správy zásad skupiny můžete například omezit typy aplikací, které se dají na virtuálním počítači spustit.

![Zjednodušená správa virtuálních počítačů Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Pojďme se podívat na běžný ukázkový scénář. Protože servery a jiná infrastruktura dosahují konce životnosti, chce společnost Contoso přesunout aplikace, které jsou aktuálně hostované místně, do cloudu. Jejich aktuální standardní mandát pro IT servery, které hostují podnikové aplikace, musí být připojené k doméně a musí se spravovat pomocí zásad skupiny.

Správce IT společnosti Contoso má přednost před virtuálními počítači, které jsou nasazené v Azure, a usnadňuje tak správu, protože se uživatelé můžou přihlašovat pomocí svých podnikových přihlašovacích údajů. Když se připojíte k doméně, můžou se virtuální počítače nakonfigurovat taky tak, aby splňovaly požadované standardní hodnoty zabezpečení pomocí objektů zásad skupiny (GPO). Společnost Contoso nechce v Azure nasazovat, monitorovat a spravovat vlastní řadiče domény.

Azure služba AD DS je skvělým přizpůsobením tohoto případu použití. Spravovaná doména vám umožní připojit virtuální počítače k doméně, použít jedinou sadu přihlašovacích údajů a použít zásady skupiny. A vzhledem k tomu, že se jedná o spravovanou doménu, nemusíte konfigurovat a spravovat řadiče domény sami.

### <a name="deployment-notes"></a>Poznámky k nasazení

Následující pokyny k nasazení se vztahují na tento příklad případu použití:

* Spravované domény ve výchozím nastavení používají jednu strukturovanou strukturu organizační jednotky (OU). Všechny virtuální počítače připojené k doméně jsou v jedné organizační jednotce. V případě potřeby můžete vytvořit [vlastní organizační jednotky][custom-ou].
* Azure služba AD DS používá integrovaný objekt zásad skupiny pro kontejnery uživatelů a počítačů. Pro další kontrolu můžete [vytvořit vlastní objekty zásad skupiny][create-gpo] a zaměřit je na vlastní organizační jednotky.
* Azure služba AD DS podporuje základní schéma objektu počítače AD. Nemůžete rozšiřuje schéma objektu počítače.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Místní aplikace navýšení a posunutí, které používají ověřování pomocí vazeb LDAP

V ukázkovém scénáři má contoso místní aplikaci, která byla zakoupena před několika lety softwaru ISV. Aplikace je aktuálně v režimu údržby ISV a požaduje změny aplikace. Tato aplikace obsahuje webový front-end, který shromažďuje přihlašovací údaje uživatelů pomocí webového formuláře a pak ověřuje uživatele provedením vazby LDAP k místnímu služba AD DS prostředí.

![Vazba LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Společnost Contoso by chtěla migrovat tuto aplikaci do Azure. Aplikace by měla dál fungovat tak, jak jsou, bez nutnosti změn. Navíc by uživatelé měli být schopni ověřit pomocí svých stávajících podnikových přihlašovacích údajů a bez dalšího školení. Mělo by být transparentní pro koncové uživatele, kde je aplikace spuštěná.

V tomto scénáři Azure služba AD DS umožňuje aplikacím provádět vazby LDAP jako součást procesu ověřování. Starší verze místních aplikací můžou přecházet do Azure a pokračovat v bezproblémovém ověřování uživatelů bez jakýchkoli změn v konfiguraci nebo v uživatelském prostředí.

### <a name="deployment-notes"></a>Poznámky k nasazení

Následující pokyny k nasazení se vztahují na tento příklad případu použití:

* Ujistěte se, že aplikace nepotřebuje upravovat ani zapisovat do adresáře. Přístup pro zápis protokolu LDAP do spravované domény není podporován.
* Nemůžete měnit hesla přímo proti spravované doméně. Koncoví uživatelé můžou změnit heslo buď pomocí [mechanismu změny hesla samoobslužné služby Azure AD][sspr] , nebo v místním adresáři. Tyto změny jsou pak automaticky synchronizovány a k dispozici ve spravované doméně.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Místní aplikace přenesené a posunutí, které pro přístup k adresáři používají protokol LDAP

Podobně jako v předchozím ukázkovém scénáři předpokládáme, že Contoso má místní obchodní aplikaci (LOB), která byla vyvinuta skoro před desetiletí. Tato aplikace je v adresáři a byla navržena tak, aby používala protokol LDAP ke čtení informací nebo atributů uživatelů z služba AD DS. Aplikace nemění atributy nebo jinak zapisuje do adresáře.

Společnost Contoso chce migrovat tuto aplikaci do Azure a vyřadit místní hardware o splatnosti, který aktuálně hostuje tuto aplikaci. Aplikaci nelze přepsat, aby používala moderní rozhraní API adresářů, jako je Microsoft Graph rozhraní API na bázi REST. Možnost přezvednutí a posunutí je žádoucí, kde aplikace může být migrována do provozu v cloudu bez úpravy kódu nebo přepisu aplikace.

Pro pomoc s tímto scénářem Azure služba AD DS umožňuje aplikacím provádět čtení LDAP proti spravované doméně, aby získala informace o atributech, které potřebuje. Aplikaci není nutné přepsána, takže převedené a posunutí do Azure umožňuje uživatelům pokračovat v používání aplikace bez toho, aby se při spuštění spouštěla změna.

### <a name="deployment-notes"></a>Poznámky k nasazení

Následující pokyny k nasazení se vztahují na tento příklad případu použití:

* Ujistěte se, že aplikace nepotřebuje upravovat ani zapisovat do adresáře. Přístup pro zápis protokolu LDAP do spravované domény není podporován.
* Ujistěte se, že aplikace nepotřebuje vlastní/rozšířené schéma služby Active Directory. Rozšíření schématu nejsou v Azure služba AD DS podporovaná.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrace místní služby nebo aplikace démona do Azure

Některé aplikace zahrnují několik vrstev, kde jedna z vrstev musí provádět ověřená volání do back-endové vrstvy, jako je například databáze. V těchto scénářích se běžně používají účty služby AD. Když do Azure převedete aplikace, Azure služba AD DS vám umožní dál používat účty služby stejným způsobem. Můžete se rozhodnout použít stejný účet služby synchronizovaný z místního adresáře do služby Azure AD nebo vytvořit vlastní organizační jednotku a pak v této organizační jednotce vytvořit samostatný účet služby. V obou případech aplikace i nadále fungují stejným způsobem, aby bylo možné provádět ověřená volání na jiné úrovně a služby.

![Účet služby pomocí WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

V tomto ukázkovém scénáři má Contoso vlastní aplikaci trezoru softwaru, která zahrnuje webový front-end, SQL Server a back-end server FTP. Ověřování v integrovaném systému Windows pomocí účtů služeb ověřuje webový front-end na server FTP. Webový front-end je nastaven tak, aby běžel jako účet služby. Back-end Server je nakonfigurován pro autorizaci přístupu z účtu služby pro webový front-end. Společnost Contoso nechce nasadit a spravovat vlastní virtuální počítače řadičů domény v cloudu, aby bylo možné tuto aplikaci přesunout do Azure.

V tomto scénáři se servery hostující webový front-end, SQL Server a server FTP můžou migrovat na virtuální počítače Azure a připojit se ke spravované doméně. Virtuální počítače potom můžou použít stejný účet služby v místním adresáři pro účely ověřování aplikace, které se synchronizují prostřednictvím služby Azure AD pomocí Azure AD Connect.

### <a name="deployment-notes"></a>Poznámky k nasazení

Následující pokyny k nasazení se vztahují na tento příklad případu použití:

* Ujistěte se, že aplikace pro ověřování používají uživatelské jméno a heslo. Služba Azure služba AD DS nepodporuje certifikát nebo ověřování pomocí čipové karty.
* Nemůžete měnit hesla přímo proti spravované doméně. Koncoví uživatelé můžou změnit heslo buď pomocí [mechanismu změny hesla samoobslužné služby Azure AD][sspr] , nebo v místním adresáři. Tyto změny jsou pak automaticky synchronizovány a k dispozici ve spravované doméně.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Nasazení služby Vzdálená plocha systému Windows Server v Azure

Pomocí Azure služba AD DS můžete poskytovat spravované doménové služby na servery vzdálené plochy nasazené v Azure.

Další informace o tomto scénáři nasazení najdete v tématu věnovaném [integraci Azure AD Domain Services s nasazením služby Vzdálená][windows-rds]plocha.

## <a name="domain-joined-hdinsight-clusters"></a>Clustery HDInsight připojené k doméně

Můžete nastavit cluster Azure HDInsight, který je připojený ke spravované doméně s povoleným Apache Ranger. Pomocí nástrojů založených na rozhraní ODBC, jako je Excel nebo Tableau, můžete vytvořit a použít zásady pro podregistr prostřednictvím Apache Ranger a uživatelům, jako jsou třeba odborníci přes data, se připojit k podregistru. I nadále pracujeme na přidání dalších úloh, jako jsou například HBA, Spark a zatížení, do HDInsight připojené k doméně.

Další informace o tomto scénáři nasazení najdete v tématu [konfigurace clusterů HDInsight připojených k doméně][hdinsight] .

## <a name="next-steps"></a>Další kroky

Začněte tím, [že vytvoříte a nakonfigurujete Azure Active Directory Domain Services spravovanou doménu][tutorial-create-instance].

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[tutorial-create-instance]: tutorial-create-instance.md
[custom-ou]: create-ou.md
[create-gpo]: manage-group-policy.md
[sspr]: ../active-directory/authentication/overview-authentication.md#self-service-password-reset
[compare]: compare-identity-solutions.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds