---
title: 'Azure Active Directory Domain Services: Scénáře nasazení | Dokumentace Microsoftu'
description: Scénáře nasazení pro Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2017
ms.author: ergreenl
ms.openlocfilehash: 0659586512b36c51c5058271fa5e1bdb46efbc3b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193280"
---
# <a name="deployment-scenarios-and-use-cases"></a>Scénáře nasazení a případy použití
V této části se podíváme na několik scénáře a případy použití, které využívají samosprávné domény služby Azure Active Directory (AD).

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Zabezpečení a snadnou správu virtuálních počítačů Azure
Azure Active Directory Domain Services můžete použít ke správě virtuálních počítačů Azure ve zjednodušené způsobem. Virtuální počítače Azure může být připojen k spravované doméně, což umožní Přihlaste se pomocí svých podnikových přihlašovacích údajů AD. Tento přístup pomáhá zabránit starostí o správu přihlašovacích údajů jako je například Správa účtů místního správce na všech virtuálních počítačů Azure.

Server virtual machines s, která jsou připojená do spravované domény mohou také spravovat a zabezpečit pomocí zásad skupiny. Můžete používat směrné plány zabezpečení vyžaduje na virtuální počítače Azure a zamezit je v souladu s pokyny pro podnikové zabezpečení. Například můžete použít možnosti správy zásad skupiny k omezení typů aplikací, které se dají spouštět na těchto virtuálních počítačích.

![Zjednodušená správa virtuálních počítačů Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Jako servery a další infrastrukturu dosáhne ukončenou životností, Contoso přesouvá mnoho aplikací, které jsou momentálně hostované v místním prostředí do cloudu. Aktuální úroveň IT Určuje, že servery, které hostují firemní aplikace musí být připojené k doméně a spravované pomocí zásad skupiny. Společnosti Contoso IT správce upřednostňuje domény připojení virtuálních počítačů nasazených v Azure, a usnadnit správu. V důsledku toho správci a uživatelé můžou přihlásit pomocí svých podnikových přihlašovacích údajů. Ve stejnou dobu může být počítače nakonfigurované na splňovat požadovaná pravidla pomocí zásad skupiny. Contoso přejete není potřeba nasazovat, monitorovat a spravovat řadiče domény v Azure k zabezpečení virtuálních počítačů Azure. Azure AD Domain Services je tedy skvěle hodí pro tento případ použití.

**Poznámky k nasazení**

Vezměte v úvahu následující důležité skutečnosti pro tento scénář nasazení:

* Ve výchozím nastavení spravovaných domén, které poskytuje Azure AD Domain Services poskytují struktura single plochý OU (organizační jednotka). Všechny počítače připojené k doméně se nacházejí v jedné organizační jednotce bez stromové struktury. Můžete ale vytvořit vlastní organizační jednotky.
* Azure AD Domain Services podporuje jednoduchý zásad skupiny ve formuláři integrovaného objektu zásad skupiny jednotlivých uživatelů a počítačů kontejnery. Můžete vytvořit vlastní objekty zásad skupiny a cílit na vlastní organizační jednotky.
* Azure AD Domain Services podporuje základní schéma objektu počítače AD. Objekt počítače schéma nelze rozšířit.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Lift and shift místní aplikaci, která používá ověřování vazby LDAP se službami infrastruktury Azure
![Vázání protokolu LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso má místní aplikace, který byl zakoupen od nezávislým výrobcem softwaru mnoha lety. Aplikace je aktuálně v režimu údržby nezávislí a vyžádání změn do aplikace je nepřekonatelně drahé pro doménu Contoso. Tato aplikace má webový front-end, který shromažďuje přihlašovací údaje uživatele pomocí webového formuláře a pak ověřuje uživatele pomocí vazeb LDAP ke podnikové služby Active Directory. Contoso chce migrovat tato aplikace se službami infrastruktury Azure. Je žádoucí, zda aplikace funguje, jak jsou, bez nutnosti změny. Uživatelé by měli být navíc k ověřování pomocí jejich existujícími podnikovými přihlašovacími údaji a bez nutnosti přeučování uživatelům provádět věci jinak. Jinými slovy koncoví uživatelé by měl být oblivious o kterém aplikace běží a migrace by měla být transparentní k nim.

**Poznámky k nasazení**

Vezměte v úvahu následující důležité skutečnosti pro tento scénář nasazení:

* Ujistěte se, že aplikace není potřeba upravovat a zápis do adresáře. LDAP k zápisu do spravovaných domén, které poskytuje Azure AD Domain Services se nepodporuje.
* Nelze změnit hesla přímo na spravované doméně. Koncoví uživatelé si můžou změnit heslo buď pomocí mechanismu Změna hesla pomocí samoobslužné služby Azure AD nebo v místním adresáři. Tyto změny jsou automaticky synchronizované a k dispozici ve spravované doméně.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Přečtěte si místní aplikaci, která využívá LDAP Lift and shift pro přístup k adresáři se službami infrastruktury Azure
Contoso má místní aplikace – obchodní (LOB), který byl vyvinut téměř objevil před deseti lety. Tato aplikace je adresář vědět a byla navržena pro spolupráci s AD na Windows serveru. Aplikace používá ke čtení informace/atributy uživatelů ze služby Active Directory protokolu LDAP (Lightweight Directory Access Protocol). Aplikace neupravuje atributy nebo jinak zapisovat do adresáře. Contoso chtěli migrovat tato aplikace se službami infrastruktury Azure a vyřazení místní hardware stárnoucích aktuálně hostitelem této aplikace. Aplikace nemůže být přepsán používat moderní adresáře rozhraní API, jako je například Azure AD Graph API založené na protokolu REST. Proto možnost lift and shift je žádoucí, kterým je možné migrovat aplikace na spouštění v cloudu, bez úpravy kódu nebo přepsání aplikace.

**Poznámky k nasazení**

Vezměte v úvahu následující důležité skutečnosti pro tento scénář nasazení:

* Ujistěte se, že aplikace není potřeba upravovat a zápis do adresáře. LDAP k zápisu do spravovaných domén, které poskytuje Azure AD Domain Services se nepodporuje.
* Ujistěte se, že aplikace není nutné vlastní nebo rozšířit schéma služby Active Directory. Rozšíření schématu nejsou podporovány ve službě Azure AD Domain Services.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrovat místní služby nebo démona aplikace se službami infrastruktury Azure
Některé aplikace skládají z více vrstev, kde jednu z úrovní potřebuje k provádění ověřených volání do back-endovou vrstvou, jako je například databázová vrstva. Účty služby Active Directory se obvykle používají pro tyto případy použití. Můžete si lift and shift těchto aplikací pro služby infrastruktury Azure a použití Azure AD Domain Services pro potřeby identity z těchto aplikací. Můžete použít stejný účet služby, který je synchronizované z vašeho místního adresáře do služby Azure AD. Alternativně můžete nejdřív vytvořit vlastní organizační jednotky a pak vytvořte samostatný účet služby v dané organizační jednotce, k nasazení těchto aplikací.

![Účet služby pomocí WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso má vlastními silami sestavených softwarová aplikace trezor, který zahrnuje webového front-endu, SQL server a serveru back-end serveru FTP. Ověření integrované Windows účtů služeb se používá k ověření webového front-endu k serveru FTP. Spustit jako účet služby je nastavení webového front-endu. Back-end server je nakonfigurován k autorizaci přístupu z účtu služby pro webového front-endu. Contoso upřednostňuje, aby nasazení virtuálního počítače řadiče domény v cloudu po přesunutí této aplikace se službami infrastruktury Azure. Společnosti Contoso správce IT můžete nasadit servery, které hostují webového front-endu, SQL server a server FTP na virtuálních počítačích Azure. Tyto počítače jsou pak připojeny k spravované doméně služby Azure AD Domain Services. Pak můžou použít stejný účet služby v jejich místní adresář pro účely ověřování aplikace. Tento účet služby se synchronizují do spravované domény služby Azure AD Domain Services a je k dispozici pro použití.

**Poznámky k nasazení**

Vezměte v úvahu následující důležité skutečnosti pro tento scénář nasazení:

* Ujistěte se, že aplikace používá k ověření uživatelského jména a hesla. Ověřování na základě certifikátů nebo čipová karta není podporována službou Azure AD Domain Services.
* Nelze změnit hesla přímo na spravované doméně. Koncoví uživatelé si můžou změnit heslo buď pomocí mechanismu Změna hesla pomocí samoobslužné služby Azure AD nebo v místním adresáři. Tyto změny jsou automaticky synchronizované a k dispozici ve spravované doméně.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Windows Server vzdálené plochy nasazení v Azure
Azure AD Domain Services můžete použít k poskytování spravované domény služby AD na vzdálené plochy servery nasazené v Azure.

Další informace o tomto scénáři nasazení najdete v tématu Jak [integrovat Azure AD Domain Services v rámci vašeho nasazení vzdálené plochy](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Clustery HDInsight připojené k doméně (Preview)
Můžete nastavit cluster Azure HDInsight, který je připojen k spravované doméně služby Azure AD Domain Services s Apache Rangerem povolena. Vytvořit a použít zásady Hivu pomocí Apache Ranger a umožnit uživatelům (například odborníci přes data) pro připojení k Hivu pomocí rozhraní ODBC nástrojů, například Excel, Tableau atd. Microsoft pracuje na přidávání dalších úloh, jako je například HBase a Spark, Storm, připojených k doméně HDInsight brzy.

Další informace o tomto scénáři nasazení najdete v tématu Jak [konfigurace clusterů HDInsight připojených k doméně](../hdinsight/domain-joined/apache-domain-joined-configure.md)
