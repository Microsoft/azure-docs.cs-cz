---
title: Azure AD Connect v Microsoft Cloudu Německo
description: Azure AD Connect integruje vaše místní adresáře do služby Azure Active Directory. To umožní poskytovat společnou identitu pro aplikace Office 365, Azure a SaaS integrované s Azure AD.
keywords: Úvod do služby Azure AD Connect, přehled služby Azure AD Connect, co je Azure AD Connect, instalace služby Active Directory, Německo, Černý les
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2bcb0caf-5d97-46cb-8c32-bda66cc22dad
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62f0d82a543c0ae4e629eda3bca18b0a06322f2c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178188"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect v Microsoft Cloudu Německo – verze Public Preview
## <a name="introduction"></a>Úvod
Azure AD Connect poskytuje synchronizaci mezi vaší místní službou Active Directory a službou Azure Active Directory.
V současné době je nutné, aby mnoho scénářů v [Microsoft Cloudu Německo](https://azure.microsoft.com/global-infrastructure/germany/
) prováděl operátor. Při použití Microsoft cloudu Německo, musí být vědomi následujících informací:

* Aby mohlo dojít k úspěšné synchronizaci, je nutné, aby na proxy serveru byly otevřené následující adresy URL:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Seznamy odvolaných certifikátů
* Při přihlášení do adresáře služby Azure AD je nutné použít účet v doméně onmicrosoft.de.

 
## <a name="download"></a>Ke stažení
Službu Azure AD Connect lze stáhnout z okna Azure AD Connect v rámci portálu.  Pomocí pokynů níže vyhledejte okno Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>Okno Azure AD Connect
Po přihlášení k webu Azure portal:

1. Přejděte do Procházet.
2. Vyberte Azure Active Directory.
3. Poté vyberte Azure AD Connect.

Zobrazí se vám tyto podrobnosti:

![Okno Azure AD Connect](./media/reference-connect-germany/germany1.png)

Následující tabulka popisuje funkce zobrazené v okně.

| Název | Popis |
| --- | --- |
| STAV SYNCHRONIZACE |Uvádí, zda je synchronizace povolená nebo zakázaná. |
| POSLEDNÍ SYNCHRONIZACE |Čas dokončení poslední úspěšné synchronizace. |
| FEDEROVANÉ DOMÉNY |Zobrazuje počet aktuálně nakonfigurovaných federovaných domén. |

## <a name="installation"></a>Instalace
Chcete-li nainstalovat službu Azure AD Connect, můžete využít dokumentaci, kterou najdete [tady](how-to-connect-install-roadmap.md).

## <a name="advanced-features-and-additional-information"></a>Pokročilé funkce a další informace
Další informace o vlastním nastavením nebo pokročilým konfiguracím, přejděte na [integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md). Tato stránka poskytuje informace a odkazy na další doprovodné materiály.

