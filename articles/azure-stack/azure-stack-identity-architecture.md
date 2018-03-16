---
title: "Architektura identity pro Azure zásobníku | Microsoft Docs"
description: "Další informace o architektuře identity, které můžete použít s Azure zásobníku."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 2/28/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 899e0fc0c1eb93d68c79c92c9cc042462ebc2fef
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="identity-architecture-for-azure-stack"></a>Architektura identity pro Azure zásobníku
Než si zvolíte zprostředkovatele identity pro použití s Azure zásobníku, pochopit důležité rozdíly mezi možnosti služby Azure Active Directory (Azure AD) a služby Active Directory Federation Services (AD FS). 

## <a name="capabilities-and-limitations"></a>Možnosti a omezení 
Zprostředkovatele identity, který zvolíte, můžete omezit možnosti, včetně podpory pro víceklientská architektura. 

  

|Funkce nebo scénáře        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Připojení k Internetu     |Ano       |Nepovinné|
|Podpora pro více klientů     |Ano       |Ne      |
|Syndikace Marketplace.       |Ano       |Ano. Vyžaduje použití [offline syndikace Marketplace](azure-stack-download-azure-marketplace-item.md#download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) nástroj.|
|Podpora služby Active Directory Authentication Library (ADAL) |Ano |Ano|
|Podpora nástrojů, jako je Azure CLI, Visual Studio a prostředí PowerShell  |Ano |Ano|
|Vytvořit objekty služby prostřednictvím portálu Azure     |Ano |Ne|
|Vytvořit objekty služby s certifikáty      |Ano |Ano|
|Vytvořit objekty služby s tajné klíče (klíče)    |Ano |Ne|
|Aplikace můžete použít službu grafu           |Ano |Ne|
|Aplikace můžete použít zprostředkovatele identity pro přihlášení |Ano |Ano. Vyžaduje aplikace vytvořit federaci s místní instance služby AD FS. |

## <a name="topologies"></a>Topologie
V následujících částech discus různé topologie identity, které můžete použít.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: Topologie jednoho klienta 
Když nainstalujete zásobník Azure a Azure AD, Azure zásobníku ve výchozím nastavení používá topologii jednoho klienta. 

Topologie jednoho klienta je užitečné, když:
- Všichni uživatelé jsou součástí stejné klienta.
- Poskytovatel služeb hostitelem instance Azure zásobníku v organizaci. 

![Azure zásobníku jednoho klienta topologie s Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Tato topologie funkce následující vlastnosti:
- Azure zásobníku zaregistruje všechny aplikace a služby pro stejnou službou Azure AD klienta adresáře. 
- Azure zásobníku ověřuje uživatele a aplikací z adresáře, včetně tokeny. 
- Identity pro správce (operátorům cloudu) a uživatelů klienta jsou ve stejném adresáři klientovi. 
- Chcete-li uživatel z jiného adresáře pro přístup k tomuto prostředí zásobníku Azure, musíte [pozvat uživatele jako Host](azure-stack-identity-overview.md#guest-users) k adresáři klienta. 

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: topologie pro více klientů
Operátoři cloudu můžete nakonfigurovat zásobník Azure pro povolení přístupu k aplikacím klienty z jednoho nebo více organizací. Uživatelé přístup k aplikacím prostřednictvím portálu user portal. V této konfiguraci portálu pro správu (používá operátor cloudu) je omezená na uživatele z jednoho adresáře. 

Topologie s více klienty je užitečné, když:
- Poskytovatel služeb chce uživatelům z několik organizací, které umožní přístup k Azure zásobníku.

![Azure zásobníku víceklientské topologie s Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Tato topologie funkce následující vlastnosti:
- Přístup k prostředkům by měla být na základě za organizace. 
- Uživatelé z jedné organizace by měli nelze udělit přístup k prostředkům pro uživatele, kteří jsou mimo organizaci. 
- Identity pro správce (operátorům cloudu) může být v samostatné directory klienta z identity pro uživatele. Toto oddělení zajišťuje izolaci účet na úrovni zprostředkovatele identity. 
 
### <a name="ad-fs"></a>AD FS  
Topologie služby AD FS je nutné, když je splněna jedna z následujících podmínek:
- Azure zásobníku se nepřipojí k Internetu.
- Azure zásobníku může připojit k Internetu, ale chcete použít pro zprostředkovatele identity služby AD FS.
  
![Azure zásobníku topologie pomocí služby AD FS](media/azure-stack-identity-architecture/adfs.png)

Tato topologie funkce následující vlastnosti:
- Pro zajištění podpory této topologie v produkčním prostředí, musíte integrovat předdefinované instanci služby Azure zásobníku AD FS s existující instancí služby AD FS, který je zálohovaný službou Active Directory, prostřednictvím vztahu důvěryhodnosti federace. 
- Službu grafu v zásobníku Azure můžete integrovat s vaší stávající instancí služby Active Directory. Můžete taky služby založené na protokolu OData rozhraní Graph API, která podporuje rozhraní API, které jsou konzistentní s Azure AD Graph API. 

  Rozhraní Graph API k interakci s vaší instancí služby Active Directory, vyžaduje pověření uživatele z vaší instance služby Active Directory, které mají oprávnění jen pro čtení. 
  - Předdefinované instanci služby AD FS je založena na systému Windows Server 2016. 
  - Vaše instance služby AD FS a služby Active Directory musí být založený na Windows Server 2012 nebo novější. 
  
  Mezi vaší instance služby Active Directory a integrované instanci služby AD FS interakce nejsou omezeny na OpenID Connect a mohou použít libovolný protokol pro vzájemně podporované. 
  - Uživatelské účty se vytváří a spravují v místní instanci služby Active Directory.
  - Objekty služby a registrace pro aplikace se spravují v předdefinované instanci Active Directory.



## <a name="next-steps"></a>Další postup
- [Identity – přehled](azure-stack-identity-overview.md)   
- [Integrace Datacenter - identity](azure-stack-integrate-identity.md)
