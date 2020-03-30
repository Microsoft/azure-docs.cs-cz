---
title: 'Azure AD Connect: Účet služby ADSync | Dokumenty společnosti Microsoft'
description: Toto téma popisuje účet služby ADSync a poskytuje osvědčené postupy týkající se účtu.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67478716"
---
# <a name="adsync-service-account"></a>Účet služby ADSync
Azure AD Connect nainstaluje místní službu, která orchestruje synchronizaci mezi službou Active Directory a službou Azure Active Directory.  Synchronizační služba Synchronizace Synchronizace Microsoft Azure AD (ADSync) běží na serveru ve vašem místním prostředí.  Pověření pro službu jsou ve výchozím nastavení nastavena v expresních instalacích, ale mohou být přizpůsobena tak, aby splňovala požadavky na zabezpečení organizace.  Tato pověření se nepoužívají k připojení k místním doménovým strukturám nebo službě Azure Active Directory.

Výběr účtu služby ADSync je důležité rozhodnutí o plánování před instalací služby Azure AD Connect.  Jakýkoli pokus o změnu přihlašovacích údajů po instalaci bude mít za následek selhání služby ke spuštění, ztráta přístupu k databázi synchronizace a selhání ověření s připojenými adresáři (Azure a AD DS).  Dokud nebudou obnovena původní pověření, nedojde k žádné synchronizaci.

## <a name="the-default-adsync-service-account"></a>Výchozí účet služby ADSync

Při spuštění na členském serveru je služba AdSync spuštěna v kontextu účtu virtuální chodních (VSA).  Z důvodu omezení produktu je při instalaci v řadiči domény vytvořen vlastní účet služby.  Pokud účet služby Expresní nastavení nesplňuje požadavky na zabezpečení vaší organizace, nasaďte Azure AD Connect výběrem možnosti Přizpůsobit.  Pak zvolte možnost účtu služby, která splňuje požadavky vaší organizace.

>[!NOTE]
>Výchozí účet služby při instalaci v řadiči domény je ve formuláři Domain\AAD_InstallationIdentifier.  Heslo pro tento účet je náhodně generováno a představuje významné problémy pro obnovení a střídání hesel.  Společnost Microsoft doporučuje přizpůsobit účet služby během počáteční instalace na řadiči domény tak, aby používal samostatný nebo skupinový účet spravované služby (sMSA / gMSA)

|Umístění Azure AD Connect|Účet služby vytvořen|
|-----|-----|
|Členský server|SLUŽBA NT\ADSync|
|Řadič domény|Doména\AAD_74dc30c01e80 (viz poznámka)|

## <a name="custom-adsync-service-accounts"></a>Vlastní účty služeb ADSync
Společnost Microsoft doporučuje spustit službu ADSync v kontextu účtu virtuální služby nebo samostatného nebo skupinového účtu spravované služby.  Správce domény se také může rozhodnout vytvořit účet služby zřízený tak, aby splňoval vaše specifické požadavky na zabezpečení organizace.   Chcete-li přizpůsobit účet služby použitý během instalace, zvolte možnost Přizpůsobit na stránce Expresní nastavení níže.   K dispozici jsou následující možnosti:

- výchozí účet – Azure AD Connect zřídí účet služby, jak je popsáno výše
- účet spravované služby – použijte samostatný nebo skupinový MSA zřízený správcem
- účet domény – použijte účet služby domény zřízený správcem

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnostika změn účtu služby ADSync
Změna přihlašovacích údajů pro službu ADSync po instalaci bude mít za následek selhání spuštění služby, ztrátu přístupu k databázi synchronizace a selhání ověření s připojenými adresáři (Azure a AD DS).  Udělení přístupu k databázi k novému účtu služby ADSync nestačí k obnovení tohoto problému. Dokud nebudou obnovena původní pověření, nedojde k žádné synchronizaci.

Služba ADSync vydá do protokolu událostí zprávu na úrovni chyb, když ji nelze spustit.  Obsah zprávy se bude lišit v závislosti na tom, zda je používána předdefinovaná databáze (localdb) nebo úplné SQL.  Následují příklady položek protokolu událostí, které mohou být k dispozici.

### <a name="example-1"></a>Příklad 1

Šifrovací klíče služby AdSync nebyly nalezeny a byly znovu vytvořeny.  Synchronizace nebude probíhat, dokud tento problém je opraven.

Při řešení tohoto problému se šifrovací klíče Microsoft Azure AD Sync stanou nepřístupnými, pokud se změní přihlašovací údaje služby AdSync.  Pokud byla pověření změněna, změňte pomocí aplikace Services účet Přihlášení zpět na původně nakonfigurovanou hodnotu (např. NT SERVICE\AdSync) a restartujte službu.  Tím se okamžitě obnoví správný provoz služby AdSync.

Další informace naleznete v následujícím [článku.](https://go.microsoft.com/fwlink/?linkid=2086764)

### <a name="example-2"></a>Příklad 2

Službu nelze spustit, protože nebylo možné navázat připojení k místní databázi (localdb).

Při řešení tohoto problému služba Microsoft Azure AD Sync ztratí oprávnění k přístupu k místnímu poskytovateli databáze, pokud dojde ke změně přihlašovacích údajů služby AdSync.  Pokud byla pověření změněna, použijte aplikaci Services a změňte přihlašovací účet zpět na původně nakonfigurovanou hodnotu (např. NT SERVICE\AdSync) a restartujte službu.  Tím se okamžitě obnoví správný provoz služby AdSync.

Další informace naleznete v následujícím [článku.](https://go.microsoft.com/fwlink/?linkid=2086764)

Další podrobnosti Zprostředkovatel vrátil následující informace o chybě:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).