---
title: 'Azure AD Connect: Účet služby ADSync | Dokumentace Microsoftu'
description: Toto téma popisuje účtu službu ADSync a seznamuje s osvědčenými postupy týkající se účtu.
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
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478716"
---
# <a name="adsync-service-account"></a>Účet služby ADSync
Azure AD Connect nainstaluje na místní službu, která orchestruje synchronizace mezi službami Active Directory a Azure Active Directory.  Synchronizační služba Microsoft Azure AD Sync (ADSync) běží na serveru ve vašem místním prostředí.  Přihlašovací údaje pro službu jsou nastaveny ve výchozím nastavení v zařízení Express, ale lze přizpůsobit podle požadavků vaší organizace na zabezpečení.  Tyto přihlašovací údaje nejsou používány k připojení k Azure Active Directory nebo místními doménovými strukturami.

Volba ADSync účet služby je důležité plánování rozhodnutí provést před instalací Azure AD Connect.  Žádný pokus o změnit pověření po instalaci bude mít za následek selhání spuštění, služby ztráty přístupu k databázi synchronizace a kteří neprovádějí ověřování pomocí vašich připojených adresářů (Azure a služby AD DS).  Žádné synchronizaci nedojde, dokud budou obnoveny původních přihlašovacích údajů.

## <a name="the-default-adsync-service-account"></a>Výchozí účet služby ADSync

Při spuštění na členském serveru, službu AdSync spouští v kontextu systému virtuální služby účtu (Atribut).  Kvůli omezením produktu vlastní účet služby se vytvoří při instalaci na řadič domény.  Pokud účet služby Expresní nastavení nesplňuje požadavky vaší organizace na zabezpečení, pokud vyberete možnost vlastní nasazení služby Azure AD Connect.  Zvolte si možnost účet služby, která splňuje požadavky vaší organizace.

>[!NOTE]
>Výchozí účet služby při instalaci na řadič domény je Domain\AAD_InstallationIdentifier ve formuláři.  Heslo pro tento účet je náhodně generované a prezentuje zaměřující se problematiku pro rotaci obnovení a heslo.  Společnost Microsoft doporučuje přizpůsobení účtu služby při počáteční instalaci na řadič domény použít buď samostatně nebo skupinový účet spravované služby (sMSA / gMSA)

|Umístění Azure AD Connect|Vytvoření účtu služby|
|-----|-----|
|Členský Server|NT SERVICE\ADSync|
|Řadič domény|Domain\AAD_74dc30c01e80 (viz poznámky)|

## <a name="custom-adsync-service-accounts"></a>Vlastní účty služeb ADSync
Společnost Microsoft doporučuje spuštěná ADSync service v rámci účet virtuální služby nebo samostatný nebo skupinový účet spravované služby.  Správce domény si také vytvořit účet služby pro splnění požadavků na zabezpečení pro konkrétní organizace.   Přizpůsobení účtu služby používaného během instalace, zvolte možnost přizpůsobit nastavení Express obrazovce níže.   Jsou k dispozici následující možnosti:

- Výchozí účet – Azure AD Connect bude poskytovat přístup k účtu služby, jak je popsáno výše
- Spravovat účet služby – použijte samostatný nebo skupinového účtu spravované zřízené správcem
- účet domény – použijte účet zřídit vaším správcem služby domény

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnostika ADSync změny účtu služby
Změna přihlašovacích údajů pro službu ADSync po instalaci bude mít za následek selhání spuštění, služby, ztráty přístupu k databázi synchronizace a kteří neprovádějí ověřování pomocí vašich připojených adresářů (Azure a služby AD DS).  Udělení přístupu k databázi do nového účtu služby ADSync není dostatečná k zotavení z tohoto problému. Žádné synchronizaci nedojde, dokud budou obnoveny původních přihlašovacích údajů.

Službu ADSync vydá chybovou zprávu úrovně do protokolu událostí po nelze spustit.  Obsah zprávy se liší v závislosti na tom, zda je používán integrovanou databází (localdb) nebo úplný SQL.  Následují příklady položek protokolu událostí, které mohou být k dispozici.

### <a name="example-1"></a>Příklad 1

Šifrovací klíče AdSync service se nepovedlo najít a byly znovu vytvořeny.  K synchronizaci nedojde, dokud nebude tento problém je vyřešen.

Řešení potíží s Tento problém Microsoft Azure AD Sync šifrovací klíče se stane nedostupný, pokud se změní protokol pověření service AdSync.  Pokud přihlašovací údaje byly změněny, použijte aplikace služby změnit přihlašovací účet zpět na původně nakonfigurovanou hodnotu (např. NT SERVICE\AdSync) a restartujte službu.  Tato akce obnoví okamžitě správné fungování AdSync service.

Podrobnosti najdete na následujících [článku](https://go.microsoft.com/fwlink/?linkid=2086764) pro další informace.

### <a name="example-2"></a>Příklad 2

Služba nemohla spustit, protože nelze navázat připojení k místní databázi (localdb).

Odstraňování potíží se službou tento problém Microsoft Azure AD Sync, dojde ke ztrátě oprávnění pro přístup k poskytovateli místní databázi, pokud se změní protokol pověření service AdSync.  Pokud se přihlašovací údaje změnily pomocí aplikace služby změnit přihlašovací účet zpět na původně nakonfigurovanou hodnotu (např. NT SERVICE\AdSync) a restartujte službu.  Tato akce obnoví okamžitě správné fungování AdSync service.

Podrobnosti najdete na následujících [článku](https://go.microsoft.com/fwlink/?linkid=2086764) pro další informace.

Další podrobnosti vrátil následující informace o chybě zprostředkovatele:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Další postup
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).