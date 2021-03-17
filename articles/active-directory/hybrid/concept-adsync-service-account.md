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
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dddfb8426b769c06cb5b7494431b7eee34dbf9e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410891"
---
# <a name="adsync-service-account"></a>Účet služby ADSync
Azure AD Connect nainstaluje místní službu, která orchestruje synchronizaci mezi službou Active Directory a Azure Active Directory.  Služba synchronizace Microsoft Azure AD Sync (ADSync) běží na serveru ve vašem místním prostředí.  Přihlašovací údaje služby se ve výchozím nastavení standardně nastavují v expresních instalacích, ale dají se přizpůsobit tak, aby splňovaly požadavky na zabezpečení vaší organizace.  Tyto přihlašovací údaje se nepoužívají pro připojení k místním doménovým strukturám nebo Azure Active Directory.

Výběr účtu služby ADSync je důležitým rozhodnutím pro plánování, které je potřeba provést před instalací Azure AD Connect.  Všechny pokusy o změnu přihlašovacích údajů po instalaci způsobí, že se služba nedaří spustit, ztratí přístup k databázi synchronizace a nedaří se ověřování s připojenými adresáři (Azure a služba AD DS).  Dokud nebudou obnoveny původní přihlašovací údaje, nebude provedena žádná synchronizace.

## <a name="the-default-adsync-service-account"></a>Výchozí účet služby ADSync

Při spuštění na členském serveru běží služba AdSync v kontextu účtu virtuální služby (VSA).  Kvůli omezení produktu se vlastní účet služby vytvoří, když je nainstalovaný na řadiči domény.  Pokud účet služby expresních nastavení nesplňuje požadavky na zabezpečení vaší organizace, nasaďte Azure AD Connect výběrem možnosti přizpůsobit.  Pak zvolte možnost účet služby, která splňuje požadavky vaší organizace.

>[!NOTE]
>Výchozí účet služby, když je nainstalován v řadiči domény, má formát doména \ AAD_InstallationIdentifier.  Heslo pro tento účet je náhodně vygenerováno a přináší významné výzvy pro obnovení a otočení hesla.  Microsoft doporučuje přizpůsobit účet služby během počáteční instalace na řadiči domény tak, aby používal samostatný nebo skupinový účet spravované služby (sMSA/gMSA).

|Umístění Azure AD Connect|Účet služby byl vytvořen.|
|-----|-----|
|Členský server|NT SERVICE\ADSync|
|Řadič domény|Doména \ AAD_74dc30c01e80 (viz poznámka)|

## <a name="custom-adsync-service-accounts"></a>Vlastní účty služby ADSync
Společnost Microsoft doporučuje spustit službu ADSync v kontextu buď účtu virtuální služby, nebo samostatného účtu spravované služby nebo skupiny.  Správce domény může také vytvořit účet služby zřízený tak, aby splňoval vaše konkrétní požadavky na zabezpečení organizace.   Chcete-li upravit účet služby používaný při instalaci, vyberte na stránce expresní nastavení níže možnost přizpůsobit.   Dostupné jsou tyto možnosti:

- výchozí účet – Azure AD Connect zřídí účet služby, jak je popsáno výše.
- účet spravované služby – použijte samostatného nebo skupinové MSA zřízené správcem.
- doménový účet – použijte účet doménové služby zřízený správcem.

![Snímek obrazovky se stránkou nastavení Azure AD Connect Express s přepínači "přizpůsobit" nebo "použít expresní nastavení".](media/concept-adsync-service-account/adsync1.png)

![Snímek obrazovky Azure AD Connect stránce "instalace požadovaných součástí" s možností použít stávající účet spravované služby byl vybrán.](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnostikování změn účtu služby ADSync
Změna přihlašovacích údajů pro službu ADSync po instalaci způsobí, že se služba nebude spouštět, ztratí přístup k databázi synchronizace a neproběhne ověřování s připojenými adresáři (Azure a služba AD DS).  Udělení přístupu k databázi pro nový účet služby ADSync není pro obnovení z tohoto problému dostatečné. Dokud nebudou obnoveny původní přihlašovací údaje, nebude provedena žádná synchronizace.

Služba ADSync vydá do protokolu událostí zprávu o chybě, když se nemůže spustit.  Obsah zprávy se bude lišit v závislosti na tom, zda se používá integrovaná databáze (LocalDB) nebo úplný SQL Server.  Následují příklady položek protokolu událostí, které mohou být k dispozici.

### <a name="example-1"></a>Příklad 1

Šifrovací klíče služby AdSync se nepovedlo najít a znovu se vytvořily.  K synchronizaci nedojde, dokud nebude tento problém opraven.

Poradce při potížích s tímto problémem se Microsoft Azure AD synchronizace šifrovacích klíčů stane nedostupným, pokud se změní přihlašovací údaje služby AdSync.  Pokud se přihlašovací údaje změnily, pomocí aplikace služby změňte účet pro přihlášení zpátky na původně konfigurovanou hodnotu (např. NT SERVICE\AdSync) a restartujte službu.  Tím se okamžitě obnoví správná operace služby AdSync.

Další informace najdete v následujícím [článku](./whatis-hybrid-identity.md) .

### <a name="example-2"></a>Příklad 2

Službu nebylo možné spustit, protože nebylo možné navázat připojení k místní databázi (LocalDB).

Poradce při potížích s tímto problémem ztratí služba Microsoft Azure AD Sync oprávnění k přístupu k poskytovateli místní databáze, pokud se změní přihlašovací údaje služby AdSync Service.  Pokud se přihlašovací údaje změnily, pomocí aplikace služby změňte účet pro přihlášení zpátky na původně konfigurovanou hodnotu (např. NT SERVICE\AdSync) a restartujte službu.  Tím se okamžitě obnoví správná operace služby AdSync.

Další informace najdete v následujícím [článku](./whatis-hybrid-identity.md) .

Další podrobnosti vrátil zprostředkovatel následující informace o chybě:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).