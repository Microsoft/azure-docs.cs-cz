---
title: 'Azure Active Directory Domain Services: Scénáře nasazení | Microsoft Docs'
description: Scénáře nasazení služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c5216ec9-4c4f-4b7e-830b-9d70cf176b20
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: maheshu
ms.openlocfilehash: be8d565ea16849302b743e5619afd3f21703bd1e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218682"
---
# <a name="deployment-scenarios-and-use-cases"></a>Scénáře nasazení a případy použití
V této části se podíváme na několik scénářů a případy použití využívající Azure Active Directory (AD) Domain Services.

## <a name="secure-easy-administration-of-azure-virtual-machines"></a>Zabezpečení a snadné správy virtuální počítače Azure
Azure Active Directory Domain Services můžete použít k efektivní způsobem spravovat virtuální počítače Azure. Virtuální počítače Azure je možné připojit k spravované doméně, a umožňuje používat svoje podnikové přihlašovací údaje AD k přihlášení. Tento přístup umožňuje vyhnout se přihlašovací údaje správy problémů, jako je například Správa účtů místního správce na všech virtuálních počítačích Azure.

Virtuální počítače serveru, které jsou připojeny k spravované doméně lze také spravovat a zabezpečit pomocí zásad skupiny. Můžete používat směrné plány zabezpečení požadované na virtuálních počítačích Azure a zamknout je v souladu s pokyny pro podnikové zabezpečení. Například můžete použít možnosti správy zásad skupiny omezil typy aplikací, které může být spuštěn na těchto virtuálních počítačů.

![Zjednodušená správa virtuálních počítačů Azure](./media/active-directory-domain-services-scenarios/streamlined-vm-administration.png)

Jako servery a další infrastrukturou dosáhne ukončenou životností, přechází Contoso mnoho aplikací, které jsou teď hostované na místní do cloudu. Jejich aktuální standard IT vyžaduje, aby servery, které hostují firemní aplikace musí být připojený k doméně a spravovaná pomocí zásad skupiny. Společnosti Contoso správce IT upřednostní domény připojení k virtuálním počítačům nasazené v Azure, v zájmu snazší správy. V důsledku toho správcům a uživatelům se přihlásit pomocí svých podnikových přihlašovacích údajů. Ve stejnou dobu může být počítače nakonfigurované na dodržovat standardní hodnoty požadované bezpečnostní pomocí zásad skupiny. Contoso by raději nechcete mít k nasazení, monitorování a správě řadiče domény v Azure k zabezpečení virtuální počítače Azure. Azure AD Domain Services je proto skvělé přizpůsobit pro tento případ použití.

**Poznámky k nasazení**

Mějte na paměti následující důležité skutečnosti pro tento scénář nasazení:

* Ve výchozím nastavení spravovaných domén, které poskytuje Azure AD Domain Services poskytují struktura single ploché organizační jednotky (organizační jednotka). Všechny počítače připojené k doméně se nacházejí v jedné organizační jednotce ploché. Můžete ale vytvořit vlastní organizační jednotky.
* Azure AD Domain Services podporuje jednoduchý zásad skupiny ve formě integrované objektu zásad skupiny každý pro uživatele a počítače kontejnery. Můžete vytvořit vlastní objekty zásad skupiny a směrovat je vlastní organizační jednotky.
* Azure AD Domain Services podporuje základní schématu objektu počítače AD. Objekt počítače schéma nelze rozšířit.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-bind-authentication-to-azure-infrastructure-services"></a>Navýšení a shift místní aplikace, která používá ověřování vazby LDAP do služby infrastruktury Azure
![Vázání protokolu LDAP](./media/active-directory-domain-services-scenarios/ldap-bind.png)

Contoso má místní aplikace, který byl zakoupen od nezávislý dodavatel softwaru mnoha lety. Aplikace je aktuálně v režimu údržby pomocí ISV a žádají o změny aplikace je výtažkovými pro společnost Contoso. Tato aplikace má front-end založené na webu, který shromažďuje přihlašovací údaje uživatele pomocí webového formuláře a potom ověřuje uživatele pomocí vazby protokolu LDAP do podnikové služby Active Directory. Contoso chcete migrovat služby infrastruktury Azure v této aplikaci. Je žádoucí, že aplikace funguje podle jsou, bez nutnosti změny. Kromě toho uživatelé by měli být přístup ověřit pomocí jejich existující podnikové přihlašovací údaje a bez nutnosti přeučování uživatelům provádět akce, jinak. Jinými slovy koncoví uživatelé by měli být oblivious o kterém aplikace běží a migrace by měl být průhledná k nim.

**Poznámky k nasazení**

Mějte na paměti následující důležité skutečnosti pro tento scénář nasazení:

* Ujistěte se, že aplikace není nutné upravit nebo zápisu do adresáře. LDAP oprávnění k zápisu do spravovaných domén, které poskytuje Azure AD Domain Services nepodporuje.
* Nelze změnit hesla přímo na spravované domény. Koncoví uživatelé můžou změnit heslo, buď pomocí mechanismus pro změnu hesla pomocí samoobslužné služby Azure AD nebo s místním adresářem. Tyto změny jsou automaticky synchronizované a k dispozici ve spravované doméně.

## <a name="lift-and-shift-an-on-premises-application-that-uses-ldap-read-to-access-the-directory-to-azure-infrastructure-services"></a>Navýšení a shift místní aplikace, která používá LDAP čtení pro přístup k adresáři službám infrastruktury Azure
Contoso má místní obchodní (LOB) aplikace, která byla vyvinuta téměř deset před. Tato aplikace je adresář clustery a fungují s Windows serverem AD. Aplikace používá protokolu LDAP (Lightweight Directory Access Protocol) ke čtení informací nebo atributy o uživatelů ze služby Active Directory. Aplikace není upravit atributy nebo jinak zapisovat do adresáře. Contoso chcete migrovat tuto aplikaci do služby infrastruktury Azure a vyřazení místní hardware stárnutí aktuálně hostuje tuhle aplikaci. Aplikace nemůže být přepsané téma, které používají moderní directory rozhraní API, například Azure AD Graph API založené na REST. Proto možnost navýšení a shift se požaduje, jímž aplikace se dají migrovat na spouštění v cloudu, bez úpravy kódu nebo přepsání aplikace.

**Poznámky k nasazení**

Mějte na paměti následující důležité skutečnosti pro tento scénář nasazení:

* Ujistěte se, že aplikace není nutné upravit nebo zápisu do adresáře. LDAP oprávnění k zápisu do spravovaných domén, které poskytuje Azure AD Domain Services nepodporuje.
* Ujistěte se, že aplikace není nutné vlastní nebo rozšířené schéma služby Active Directory. Rozšíření schématu nejsou podporovány v Azure AD Domain Services.

## <a name="migrate-an-on-premises-service-or-daemon-application-to-azure-infrastructure-services"></a>Migrovat místní služba nebo démon aplikace do služby infrastruktury Azure
Některé aplikace obsahovat několik vrstev, kde jeden z vrstvy potřebuje provést ověřených volání do vrstvy back-end, jako je například a databázové vrstvy. Účty služby Active Directory se běžně používají pro tyto případy použití. Můžete navýšení a shift takové aplikace služby infrastruktury Azure a používání Azure AD Domain Services pro potřeby identity z těchto aplikací. Můžete použít stejný účet služby, který je synchronizován z vašeho místního adresáře do Azure AD. Alternativně může nejprve vytvořit vlastní organizační jednotky a poté vytvořit samostatný účet služby v dané organizační jednotce, k nasazení těchto aplikací.

![Účet služby pomocí WIA](./media/active-directory-domain-services-scenarios/wia-service-account.png)

Contoso má uživatelské softwarová aplikace úložiště, která zahrnuje front-end webové, SQL server a server back-end serveru FTP. Integrované v systému Windows ověřování účty služby se používá k ověření webového uživatelského rozhraní na serveru FTP. Front-end webový jsou nastaveny na Spustit jako účet služby. Back-end server je nakonfigurován k autorizaci přístupu z účtu služby pro webového uživatelského rozhraní. Contoso upřednostní není k k nasazení virtuálního počítače řadiče domény v cloudu a přesunout tuto aplikaci do služby infrastruktury Azure. Společnosti Contoso správce IT, můžete nasadit servery, které hostují webového uživatelského rozhraní, SQL server a server FTP na virtuálních počítačích Azure. Tyto počítače jsou pak připojeny k spravované doméně služby Azure AD Domain Services. Poté mohou používat stejný účet služby ve své místní adresář pro účely ověření aplikace. Tento účet služby je synchronizován k spravované doméně služby Azure AD Domain Services a je k dispozici pro použití.

**Poznámky k nasazení**

Mějte na paměti následující důležité skutečnosti pro tento scénář nasazení:

* Ujistěte se, že aplikace bude používat uživatelské jméno a heslo pro ověřování. Azure AD Domain Services nepodporuje ověřování na základě certifikátů nebo čipová karta.
* Nelze změnit hesla přímo na spravované domény. Koncoví uživatelé můžou změnit heslo, buď pomocí mechanismus pro změnu hesla pomocí samoobslužné služby Azure AD nebo s místním adresářem. Tyto změny jsou automaticky synchronizované a k dispozici ve spravované doméně.

## <a name="windows-server-remote-desktop-services-deployments-in-azure"></a>Windows Server vzdálené plochy nasazení v Azure
Azure AD Domain Services můžete použít k poskytování spravované domény služby AD na vzdálené plochy servery nasazené v Azure.

Další informace o tomto scénáři nasazení najdete v tématu Jak [integrovat Azure AD Domain Services v rámci vašeho nasazení vzdálené plochy](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-azure-adds).


## <a name="domain-joined-hdinsight-clusters-preview"></a>Clustery HDInsight připojený k doméně (Preview)
Můžete nastavit cluster služby Azure HDInsight, který je připojen k spravované doméně služby Azure AD Domain Services s Apache škálu povolena. Vytvořit a použít zásady Hive prostřednictvím Apache škálu a povolit uživatelům (například datových vědců) pro připojení k Hive pomocí rozhraní ODBC nástrojů, například aplikace Excel, Tableau atd. Společnost Microsoft pracuje na přidávání dalších úloh, jako jsou HBase, Spark a Storm, k doméně HDInsight brzy.

Další informace o tomto scénáři nasazení najdete v tématu Jak [konfigurace clusterů HDInsight připojený k doméně](../hdinsight/domain-joined/apache-domain-joined-configure.md)
