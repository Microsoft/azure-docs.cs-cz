---
title: Architektura identity pro službu Azure Stack | Dokumentace Microsoftu
description: Další informace o architektuře identitu, která vám pomůže s využitím Azure stacku.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/01/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: bf69c71a8b361e4a147263bc60324573c710818f
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412692"
---
# <a name="identity-architecture-for-azure-stack"></a>Architektura identity pro službu Azure Stack
Než se rozhodnete zprostředkovatele identity pro použití s Azure Stack, pochopit rozdíly mezi možnosti služby Azure Active Directory (Azure AD) a Active Directory Federation Services (AD FS). 

## <a name="capabilities-and-limitations"></a>Možnosti a omezení 
Zprostředkovatel identity, kterou zvolíte můžete omezit možnosti, včetně podpory pro více tenantů. 

  

|Funkce nebo scénáře        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Připojení k Internetu     |Ano       |Nepovinné|
|Podpora pro více tenantů     |Ano       |Ne      |
|Položky nabídky na webu Marketplace |Ano       |Ano. Vyžaduje použití [offline syndikace Marketplace](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) nástroj.|
|Podpora pro Active Directory Authentication Library (ADAL) |Ano |Ano|
|Podpora nástrojů, jako je Azure CLI, Visual Studio a prostředí PowerShell  |Ano |Ano|
|Vytvoření instančních objektů na webu Azure portal     |Ano |Ne|
|Vytvoření instančních objektů s certifikáty      |Ano |Ano|
|Vytvoření instančních objektů s tajnými kódy (klíče)    |Ano |Ne|
|Aplikace můžou využívat službu grafu           |Ano |Ne|
|Aplikace můžete použít zprostředkovatele identity pro přihlášení |Ano |Ano. Vyžaduje aplikacím federování s místní instancí AD FS. |

## <a name="topologies"></a>Topologie
V následujících částech discus různé topologie identity, které můžete použít.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: Topologie jednoho tenanta 
Při instalaci Azure Stack a používají službu Azure AD, Azure Stack ve výchozím nastavení používá topologii s jedním tenantem. 

Topologie s jedním tenantem je užitečné, když:
- Všichni uživatelé jsou součástí stejného tenanta.
- Poskytovatel služeb je hostitelem instance služby Azure Stack pro organizaci. 

![Azure Stack jednoho tenanta topologie s Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Tato topologie obsahuje následující vlastnosti:
- Azure Stack zaregistruje všechny aplikace a služby pro stejnou službou Azure AD tenanta adresáře. 
- Azure Stack se ověřuje jenom uživatelé a aplikace z tohoto adresáře, včetně tokenů. 
- Identity pro správce (operátorům cloudu) a tenanta uživatele jsou ve stejném tenantu Active directory. 
- Povolit uživatele z jiného adresáře pro přístup k tomuto prostředí Azure Stack, je nutné [pozvat uživatele jako hosta](azure-stack-identity-overview.md#guest-users) do tenanta adresáře. 

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: topologie pro více tenantů
Operátoři cloudu můžete nakonfigurovat služby Azure Stack umožňující přístup k aplikacím klienty z jednoho nebo více organizací. Uživatelé přístup k aplikacím prostřednictvím portálu user portal. V této konfiguraci portálu pro správu (používá operátor cloudu) je omezený na uživatele v jednom adresáři. 

Topologie s více tenanty je užitečné, když:
- Poskytovatel služeb chce umožnit uživatelům z více organizací přístup k Azure Stack.

![Azure Stack víceklientské topologie s Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Tato topologie obsahuje následující vlastnosti:
- Na základě na organizaci měli přístup k prostředkům. 
- Uživatelé z jedné organizace by měl nejde udělit přístup k prostředkům pro uživatele, kteří jsou mimo svou organizaci. 
- Identity pro správce (operátorům cloudu) může být v samostatné adresář tenanta z identity pro uživatele. Toto oddělení zajišťuje izolaci účtu na úrovni zprostředkovatele identity. 
 
### <a name="ad-fs"></a>AD FS  
Topologie služby AD FS se požaduje, pokud je splněna jedna z následujících podmínek:
- Azure Stack není připojení k Internetu.
- Azure Stack může připojit k Internetu, ale budete chtít použít pro zprostředkovatele identity služby AD FS.
  
![Azure Stack topologie pomocí služby AD FS](media/azure-stack-identity-architecture/adfs.png)

Tato topologie obsahuje následující vlastnosti:
- Chcete-li nepodporují Tato topologie v produkčním prostředí, musíte integrovat integrovanou instanci služby AD FS pro Azure Stack s existující instancí služby AD FS, který je podporovaný službou Active Directory přes důvěryhodnost federace. 
- Služba Graph ve službě Azure Stack může integrovat s vaší stávající instanci služby Active Directory. Můžete také použít službu rozhraní Graph API založené na protokolu OData podporující rozhraní API, která jsou konzistentní s Azure AD Graph API. 

  Rozhraní Graph API k interakci s vaší instancí služby Active Directory, vyžaduje přihlašovací údaje uživatele z vaší instance služby Active Directory, které mají oprávnění jen pro čtení. 
  - Integrovanou instanci služby AD FS je založená na Windows serveru 2016. 
  - Vaše instance služby AD FS a službou Active Directory musí být založený na Windows Server 2012 nebo novější. 
  
  Mezi vaší instance služby Active Directory a integrovanou instanci služby AD FS interakce nejsou omezené na OpenID Connect a jejich použití libovolného protokolu pro vzájemně podporované. 
  - Uživatelské účty se vytváří a spravují v místní instanci Active Directory.
  - Instanční objekty a registrace pro aplikace se spravují v integrovanou instanci služby Active Directory.



## <a name="next-steps"></a>Další postup
- [Přehled identity](azure-stack-identity-overview.md)   
- [Integrace datových center - identity](azure-stack-integrate-identity.md)
