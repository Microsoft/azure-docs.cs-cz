---
title: Běžné scénáře nasazení pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Seznamte se s některými běžnými scénáři a případy použití služby Azure Active Directory Domain Services, které poskytují hodnotu a splňují obchodní potřeby.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ac67ef64ca4850c6e805b5314ace856114d889a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917226"
---
# <a name="common-use-cases-and-scenarios-for-azure-active-directory-domain-services"></a>Běžné případy použití a scénáře pro službu Azure Active Directory Domain Services

Služba Azure Active Directory Domain Services (Azure AD DS) poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, protokol LDAP (LDAP) a ověřování protokolem Kerberos / NTLM. Azure AD DS integruje s vaším stávajícím tenantem Azure AD, což umožňuje uživatelům přihlásit pomocí jejich stávající přihlašovací údaje. Tyto služby domény můžete používat bez nutnosti nasazovat, spravovat a opravovat řadiče domény v cloudu, což poskytuje plynulejší přesun místních prostředků do Azure.

Tento článek popisuje některé běžné obchodní scénáře, kde Azure AD DS poskytuje hodnotu a splňuje tyto potřeby.

## <a name="secure-administration-of-azure-virtual-machines"></a>Bezpečná správa virtuálních počítačů Azure

Abychom vám mohli používat jednu sadu přihlašovacích údajů služby AD, virtuální počítače Azure se můžou připojit ke spravované doméně Azure AD DS. Tento přístup snižuje problémy se správou pověření, jako je například udržování účtů místního správce na každém virtuálním počítači nebo samostatných účtů a hesel mezi prostředími.

Virtuální počítače, které jsou připojeny ke spravované doméně Azure AD DS lze také spravovat a zabezpečit pomocí zásad skupiny. Požadované standardní hodnoty zabezpečení lze použít na virtuální chod, aby je uzamkly v souladu s podnikovými bezpečnostními pokyny. Můžete například použít možnosti správy zásad skupiny k omezení typů aplikací, které lze spustit na virtuálním počítači.

![Zjednodušená správa virtuálních počítačů Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Podívejme se na běžný příklad scénáře. Vzhledem k tomu, že servery a další infrastruktura dosáhnou konce životnosti, contoso chce přesunout aplikace aktuálně hostované místně do cloudu. Jejich současný standard IT vyžaduje, aby servery hostující podnikové aplikace byly připojeny k doméně a spravovány pomocí zásad skupiny. Správce IT společnosti Contoso by raději doménu připojit virtuálnípočítače nasazené v Azure, aby se usnadnila správa, protože uživatelé pak můžete přihlásit pomocí svých podnikových přihlašovacích údajů. Když se připojí k doméně, virtuální počítače lze také nakonfigurovat tak, aby splňovaly požadované standardní hodnoty zabezpečení pomocí objektů zásad skupiny (GPO). Contoso by raději nenasazoval, nemonitoroval a nespravoval vlastní řadiče domény v Azure.

Azure AD DS je skvělé pro tento případ použití. Spravovaná doména Azure AD DS umožňuje připojit se k virtuálním počítačům domény, použít jednu sadu přihlašovacích údajů a použít zásady skupiny. Jako spravovaná doména není nutné konfigurovat a udržovat řadiče domény sami.

### <a name="deployment-notes"></a>Poznámky k nasazení

Následující aspekty nasazení platí pro tento příklad případu použití:

* Spravované domény Azure AD DS ve výchozím nastavení používají strukturu jedné ploché organizační jednotky (OU). Všechny virtuální servery spojené s doménou jsou v jedné hlavní výmětové oblasti. V případě potřeby můžete vytvořit vlastní vous.
* Azure AD DS používá předdefinovaný objektů gpo pro uživatele a kontejnery počítačů. Pro další ovládací prvek můžete vytvořit vlastní objekty zásad skupiny a cílit je na vlastní runové objekty.
* Azure AD DS podporuje schéma základního objektu počítače služby AD. Schéma objektu počítače nelze rozšířit.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-bind-authentication"></a>Výtah a posun místních aplikací, které používají ověřování vazeb LDAP

Jako ukázkový scénář má contoso místní aplikaci, která byla zakoupena od isv před mnoha lety. Aplikace je v současné době v režimu údržby isv a požaduje změny v aplikaci je příliš drahé. Tato aplikace má webový front-end, který shromažďuje pověření uživatelů pomocí webového formuláře a pak ověřuje uživatele provedením vazby LDAP k místnímu prostředí služby AD DS.

![Vazba LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso by chtěl tuto aplikaci migrovat do Azure. Aplikace by měla pokračovat v práci tak, jak je, bez potřebných změn. Kromě toho by uživatelé měli mít možnost ověření pomocí jejich stávající chod pověření a bez další školení. Měla by být transparentní pro koncové uživatele, kde je aplikace spuštěna.

V tomto scénáři Azure AD DS umožňuje aplikacím provádět vazby LDAP jako součást procesu ověřování. Starší místní aplikace můžou do Azure přepínat a pokračovat v bezproblémovém ověřování uživatelů bez jakékoli změny konfigurace nebo uživatelského prostředí.

### <a name="deployment-notes"></a>Poznámky k nasazení

Následující aspekty nasazení platí pro tento příklad případu použití:

* Ujistěte se, že aplikace nemusí upravovat/zapisovat do adresáře. Přístup pro zápis LDAP ke spravované doméně Azure AD DS není podporován.
* Hesla nelze změnit přímo proti spravované doméně Azure AD DS. Koncoví uživatelé můžou změnit své heslo pomocí samoobslužného mechanismu změny hesla služby Azure AD nebo proti místnímu adresáři. Tyto změny jsou pak automaticky synchronizovány a k dispozici ve spravované doméně Azure AD DS.

## <a name="lift-and-shift-on-premises-applications-that-use-ldap-read-to-access-the-directory"></a>Výtah a posun místní aplikace, které používají čtení LDAP pro přístup k adresáři

Stejně jako předchozí příklad scénáře předpokládejme, že Contoso má místní obchodní (LOB) aplikace, která byla vyvinuta téměř před deseti lety. Tato aplikace je adresář vědomi a byl navržen tak, aby pomocí LDAP číst informace / atributy o uživatelích ze služby AD DS. Aplikace nemění atributy ani jinak nezapisuje do adresáře.

Contoso chce migrovat tuto aplikaci do Azure a vyřadit stárnoucí místní hardware, který je aktuálně hostitelem této aplikace. Aplikaci nelze přepsat tak, aby používala moderní rozhraní API adresáře, jako je rozhraní Microsoft Graph API založené na protokolu REST. Možnost lift-and-shift je žádoucí, kde lze aplikaci migrovat ke spuštění v cloudu, bez úpravy kódu nebo přepisování aplikace.

Chcete-li pomoci s tímto scénářem, Azure AD DS umožňuje aplikacím provádět čtení LDAP proti spravované doméně získat informace o atributu, které potřebuje. Aplikace nemusí být přepsána, takže výtah a posun do Azure umožňuje uživatelům i nadále používat aplikaci, aniž by si uvědomili, že došlo ke změně v tom, kde běží.

### <a name="deployment-notes"></a>Poznámky k nasazení

Následující aspekty nasazení platí pro tento příklad případu použití:

* Ujistěte se, že aplikace nemusí upravovat/zapisovat do adresáře. Přístup pro zápis LDAP ke spravované doméně Azure AD DS není podporován.
* Ujistěte se, že aplikace nepotřebuje vlastní nebo rozšířené schéma služby Active Directory. Rozšíření schématu nejsou ve službě Azure AD DS podporována.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure"></a>Migrace místní služby nebo aplikace daemonu do Azure

Některé aplikace obsahují více vrstev, kde jedna z vrstev potřebuje provádět ověřená volání back-endové vrstvy, jako je například databáze. Účty služby AD se běžně používají v těchto scénářích. Když zvedáte a přeevníte aplikace do Azure, Azure AD DS vám umožní nadále používat účty služeb stejným způsobem. Můžete použít stejný účet služby, který je synchronizován z místního adresáře do služby Azure AD, nebo vytvořit vlastní hlavní výutok a potom vytvořit samostatný účet služby v této hlavní výuce. S oběma přístupy aplikace nadále fungovat stejným způsobem, aby ověřování volání na jiné vrstvy a služby.

![Účet služby pomocí služby WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

V tomto příkladu scénáře má společnost Contoso vlastní softwarovou aplikaci trezoru, která obsahuje webový front-end, sql server a back-endový ftp server. Ověřování integrované systémem Windows pomocí účtů služeb ověřuje webový front-end serveru FTP. Webový front-end je nastaven tak, aby byl spuštěn jako účet služby. Back-endový server je nakonfigurován tak, aby autorizoval přístup z účtu služby pro webový front-end. Contoso nechce nasadit a spravovat své vlastní virtuální počítače řadiče domény v cloudu přesunout tuto aplikaci do Azure.

V tomto scénáři servery hostující webový front-end, SQL server a FTP server lze migrovat do virtuálních počítačů Azure a připojit se ke spravované doméně Azure AD DS. Virtuální počítače pak můžou používat stejný účet služby ve svém místním adresáři pro účely ověřování aplikace, který se synchronizuje prostřednictvím Azure AD pomocí Azure AD Connect.

### <a name="deployment-notes"></a>Poznámky k nasazení

Následující aspekty nasazení platí pro tento příklad případu použití:

* Ujistěte se, že aplikace používají uživatelské jméno a heslo pro ověřování. Azure AD DS nepodporuje ověřování pomocí certifikátu nebo čipové karty.
* Hesla nelze změnit přímo proti spravované doméně Azure AD DS. Koncoví uživatelé můžou změnit své heslo pomocí samoobslužného mechanismu změny hesla služby Azure AD nebo proti místnímu adresáři. Tyto změny jsou pak automaticky synchronizovány a k dispozici ve spravované doméně Azure AD DS.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Nasazení služeb vzdálené plochy Windows Serveru v Azure

Azure AD DS můžete použít k poskytování služeb spravované domény na serverech vzdálené plochy nasazených v Azure. Další informace o tomto scénáři nasazení najdete v tématu [jak integrovat služby Domény Azure AD s nasazením rds][windows-rds].

## <a name="domain-joined-hdinsight-clusters"></a>Clustery HDInsight přilehlé k doméně

Můžete nastavit cluster Azure HDInsight, který je spojený se spravovanou doménou Azure AD DS s povoleným Apache Rangerem. Můžete vytvářet a používat zásady Hive prostřednictvím Apache Ranger a umožnit uživatelům, jako jsou datové vědce, připojit se k Hive pomocí nástrojů založených na rozhraní ODBC, jako je Excel nebo Tableau. Pokračujeme v práci na přidání dalších úloh, jako je HBase, Spark a Storm do HDInsight uspolečnosti.

Další informace o tomto scénáři nasazení najdete v tématu [konfigurace clusterů HDInsight přilehlých k doméně.][hdinsight]

## <a name="next-steps"></a>Další kroky

Chcete-li začít, [vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services.][tutorial-create-instance]

<!-- INTERNAL LINKS -->
[hdinsight]: ../hdinsight/domain-joined/apache-domain-joined-configure.md
[tutorial-create-instance]: tutorial-create-instance.md

<!-- EXTERNAL LINKS -->
[windows-rds]: /windows-server/remote/remote-desktop-services/rds-azure-adds
