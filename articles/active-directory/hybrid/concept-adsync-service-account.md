---
title: 'Azure AD Connect: účet služby ADSync | Microsoft Docs'
description: Toto téma popisuje účet služby ADSync a poskytuje osvědčené postupy týkající se tohoto účtu.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23d79caa6964c3f61fbb84c8b8f229f475b8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722153"
---
# <a name="adsync-service-account"></a>Účet služby ADSync
Azure AD Connect nainstaluje místní službu, která orchestruje synchronizaci mezi službou Active Directory a Azure Active Directory.  Služba synchronizace Microsoft Azure AD Sync (ADSync) běží na serveru ve vašem místním prostředí.  Přihlašovací údaje služby se ve výchozím nastavení standardně nastavují v expresních instalacích, ale dají se přizpůsobit tak, aby splňovaly požadavky na zabezpečení vaší organizace.  Tyto přihlašovací údaje se nepoužívají pro připojení k místním doménovým strukturám nebo Azure Active Directory.

Výběr účtu služby ADSync je důležitým rozhodnutím pro plánování, které je potřeba provést před instalací Azure AD Connect.  Všechny pokusy o změnu přihlašovacích údajů po instalaci způsobí, že se služba nedaří spustit, ztratí přístup k databázi synchronizace a nedaří se ověřování s připojenými adresáři (Azure a služba AD DS).  Dokud nebudou obnoveny původní přihlašovací údaje, nebude provedena žádná synchronizace.

Synchronizační služba může běžet pod různými účty. Může běžet pod účtem virtuální služby (VSA), účtem spravované služby (gMSA/sMSA) nebo běžným uživatelským účtem. Podporované možnosti se změnily pomocí verze 2017. dubna a 2021. března Azure AD Connect při nové instalaci. Pokud upgradujete ze starší verze Azure AD Connect, tyto další možnosti nejsou k dispozici. 


|Typ účtu|Možnost instalace|Description| 
|-----|------|-----|
|Účet virtuální služby|Express a Custom, 2017. dubna a novější| Účet virtuální služby se používá pro všechny Expresní instalace, s výjimkou instalací na řadič domény. Pokud používáte vlastní instalaci, je výchozí možnost, pokud nepoužijete jinou možnost.| 
|Účet spravované služby|Vlastní, 2017. dubna a novější|Pokud používáte vzdálené SQL Server, doporučujeme použít skupinový účet spravované služby. |
|Účet spravované služby|Express a Custom, 2021 březen a novější|Samostatný účet spravované služby s předponou ADSyncMSA_ se vytvoří během instalace pro Expresní instalace, pokud je nainstalovaný na řadiči domény. Pokud používáte vlastní instalaci, je výchozí možnost, pokud nepoužijete jinou možnost.|
|Uživatelský účet|Express a Custom, 2017 duben až 2021 březen|Uživatelský účet s předponou AAD_ se vytvoří během instalace pro Expresní instalace, pokud je nainstalovaný na řadiči domény. Pokud používáte vlastní instalaci, je výchozí možnost, pokud nepoužijete jinou možnost.|
|Uživatelský účet|Express a Custom, 2017 březen a starší|Během instalace pro Expresní instalace se vytvoří uživatelský účet s předponou AAD_. Při použití vlastní instalace je možné zadat jiný účet.| 

>[!IMPORTANT]
> Použijete-li příkaz připojit se sestavou z 2017. března nebo staršího, neměli byste heslo resetovat na účet služby, protože systém Windows odstraní šifrovací klíče z bezpečnostních důvodů. Účet nemůžete změnit na žádný jiný účet, aniž byste museli přeinstalovat Azure AD Connect. Pokud provedete upgrade na sestavení z verze 2017. dubna nebo novější, je podporováno, abyste změnili heslo účtu služby, ale nemůžete změnit použitý účet. 

> [!IMPORTANT]
> Účet služby můžete nastavit jenom při první instalaci. Po dokončení instalace není podporována Změna účtu služby. Pokud potřebujete změnit heslo účtu služby, je to podporované a pokyny najdete [tady](how-to-connect-sync-change-serviceacct-pass.md).

Následuje tabulka výchozích, doporučených a podporovaných možností pro účet synchronizační služby. 

Legenda: 

- **Tučné** označuje výchozí možnost a ve většině případů doporučenou možnost. 
- *Kurzíva* označuje doporučenou možnost, pokud není výchozí možností. 
- Možnost, která není dostupná tučně 
- Místní účet – místní uživatelský účet na serveru 
- Doménový účet – účet uživatele domény 
- sMSA – [samostatný účet spravované služby](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA – [skupinový účet spravované služby](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

 ||**LocalDB </br> Express**|**Vlastní LocalDB/LocalSQL </br>**|**Vzdálená </br> vlastní SQL**|
|-----|-----|-----|-----|
|**počítač připojený k doméně**|**ATRIBUT**|**ATRIBUT**</br> *sMSA*</br> *gMSA*</br> Místní účet</br> Účet domény| *gMSA* </br>Účet domény|
|Řadič domény| **sMSA**|**sMSA** </br>*gMSA*</br> Účet domény|*gMSA*</br>Účet domény| 

## <a name="virtual-service-account"></a>Účet virtuální služby 

Účet virtuální služby je speciální typ spravovaného místního účtu, který nemá heslo a automaticky ho spravuje Windows. 

 ![Účet virtuální služby](media/concept-adsync-service-account/account-1.png)

Účet virtuální služby je určený k použití s scénáři, ve kterém je synchronizační modul a SQL na stejném serveru. Pokud používáte vzdálené SQL, doporučujeme místo toho použít skupinový účet spravované služby. 

Účet virtuální služby nelze použít na řadiči domény z důvodu problémů s [Windows Data Protection API (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx) . 

## <a name="managed-service-account"></a>Účet spravované služby 

Pokud používáte vzdálené SQL Server, doporučujeme použít skupinový účet spravované služby. Další informace o tom, jak připravit službu Active Directory pro skupinový účet spravované služby, najdete v tématu [Přehled skupinových účtů spravované služby](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)). 

Chcete-li použít tuto možnost, vyberte na stránce [instalovat požadované součásti](how-to-connect-install-custom.md#install-required-components) možnost **použít existující účet služby** a vyberte možnost **účet spravované služby**. 

 ![účet spravované služby](media/concept-adsync-service-account/account-2.png)

Také se podporuje použití samostatného účtu spravované služby. Lze je však použít pouze v místním počítači a není možné je používat v rámci výchozího účtu virtuální služby. 

### <a name="auto-generated-standalone-managed-service-account"></a>Automaticky generovaný samostatný účet spravované služby 

Pokud nainstalujete Azure AD Connect na řadič domény, vytvoří se v Průvodci instalací samostatný účet spravované služby (Pokud nezadáte účet, který se má použít ve vlastním nastavení). Účet je předem opraven **ADSyncMSA_** a slouží ke spuštění skutečné synchronizační služby jako. 

Tento účet je spravovaný účet domény, který nemá heslo a je automaticky spravován systémem Windows. 

Tento účet je určený pro použití s scénáři, kdy je synchronizační modul a SQL na řadiči domény. 

## <a name="user-account"></a>Uživatelský účet 

Účet místní služby se vytvoří pomocí Průvodce instalací (Pokud nezadáte účet, který se má použít ve vlastním nastavení). Účet je předem opraven AAD_ a slouží ke spuštění skutečné synchronizační služby jako. Pokud nainstalujete Azure AD Connect na řadič domény, účet se vytvoří v doméně. Účet služby AAD_ musí být v doméně umístěn v těchto případech: 
- používáte vzdálený server se systémem SQL Server 
- použijete proxy server, který vyžaduje ověření. 

 ![uživatelský účet](media/concept-adsync-service-account/account-3.png)

Účet se vytvoří s dlouhým složitým heslem, jehož platnost nevyprší. 

Tento účet se používá k bezpečnému ukládání hesel pro ostatní účty. Tyto další účty hesla se ukládají v databázi jako šifrované. Privátní klíče pro šifrovací klíče jsou chráněné pomocí šifrování tajného klíče kryptografických služeb pomocí Windows Data Protection API (DPAPI). 

Pokud používáte úplný SQL Server, účet služby je DBO databáze vytvořenou pro modul synchronizace. Služba nebude fungovat tak, jak má žádná jiná oprávnění. Vytvoří se také přihlášení SQL. 

Účtu je také uděleno oprávnění k souborům, klíčům registru a dalším objektům, které se vztahují k synchronizačnímu modulu. 


## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
