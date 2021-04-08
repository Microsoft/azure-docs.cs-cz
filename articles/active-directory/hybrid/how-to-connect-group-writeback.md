---
title: 'Azure AD Connect: zpětný zápis skupin'
description: Tento článek popisuje zpětný zápis skupin v Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c506c87ad5901754175f18e6b50bc6ed46a3c19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246906"
---
# <a name="azure-ad-connect-group-writeback"></a>Zpětný zápis skupin Azure AD Connect

Zpětný zápis skupin umožňuje zákazníkům využít cloudové skupiny pro své hybridní potřeby. Pokud používáte funkci Microsoft 365 skupiny, můžete tyto skupiny prezentovat v místní službě Active Directory. Tato možnost je dostupná **jenom** v případě, že máte Exchange ve vaší místní službě Active Directory.

## <a name="pre-requisites"></a>Požadavky
Aby bylo možné povolit zpětný zápis skupin, musí být splněny následující předpoklady.
- Azure Active Directory Premium licence pro vašeho tenanta.
- Nakonfigurované hybridní nasazení mezi vaší místní organizací Exchange a Microsoft 365 a ověření, že funguje správně.
- Nainstalovala se podporovaná verze místního Exchange.
- Nakonfigurované jednotné přihlašování pomocí Azure Active Directory Connect 

## <a name="enable-group-writeback"></a>Povolení zpětného zápisu skupin
Pokud chcete povolit zpětný zápis skupin, použijte následující postup:

1. Otevřete Průvodce Azure AD Connect, vyberte **Konfigurovat** a pak klikněte na **Další**.
2. Vyberte **přizpůsobit možnosti synchronizace** a pak klikněte na **Další**.
3. Na stránce **připojit ke službě Azure AD** zadejte svoje přihlašovací údaje. Klikněte na **Next** (Další).
4. Na stránce **volitelné funkce** ověřte, že jsou stále vybrané možnosti, které jste předtím nakonfigurovali.
5. Vyberte možnost **zpětný zápis skupiny** a klikněte na **Další**.
6. Na **stránce zpětný zápis** vyberte organizační jednotku (OU) služby Active Directory pro ukládání objektů synchronizovaných z Microsoft 365 do vaší místní organizace a pak klikněte na **Další**.
7. Na stránce **připraveno** ke konfiguraci klikněte na **Konfigurovat**.
8. Po dokončení průvodce klikněte na tlačítko **ukončit** na stránce Konfigurace byla dokončena.
9. Otevřete Windows PowerShell jako správce na serveru Azure Active Directory Connect a spusťte následující příkazy.

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

# To grant the <MSOL_account> permission to all domains in the forest:
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN

# To grant the <MSOL_account> permission to specific OU (eg. the OU chosen to writeback Office 365 Groups to):
$GroupWritebackOU = <DN of OU where groups are to be written back to>
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN -ADObjectDN $GroupWritebackOU
```

Další informace o konfiguraci skupin Microsoft 365 najdete v tématu [konfigurace Microsoft 365 skupin pomocí místní služby Exchange Hybrid](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect).

## <a name="disabling-group-writeback"></a>Zakázání zpětného zápisu skupiny
Chcete-li zakázat zpětný zápis skupiny, použijte následující postup: 


1. Spusťte Průvodce Azure Active Directory Connect a přejděte na stránku další úlohy. Vyberte úlohu **přizpůsobit možnosti synchronizace** a klikněte na **Další**.
2. Na stránce **volitelné funkce** zrušte kontrolu zpětného zápisu skupiny.  Zobrazí se upozornění, že budete mít jistotu, že se skupiny odstraní.  Klikněte na **Ano**.
   >[!IMPORTANT]
   > Zakázáním zpětného zápisu skupiny dojde k odstranění všech skupin, které byly dříve vytvořeny pomocí této funkce, z místní služby Active Directory v dalším cyklu synchronizace. 

   ![Zrušit zaškrtávací políčko](media/how-to-connect-group-writeback/group2.png)
  
3. Klikněte na **Next** (Další).
4. Klikněte na **Konfigurovat**.

 >[!NOTE]
 > Zakázáním zpětného zápisu skupiny nastavíte v konektoru Azure Active Directory úplný příznak importu a úplné synchronizace na hodnotu true, což způsobí, že se pravidlo změní na možnost rozšířit do dalšího cyklu synchronizace a odstraní skupiny, které byly dříve zapsány zpět do služby Active Directory.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
