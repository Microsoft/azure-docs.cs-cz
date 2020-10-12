---
title: Azure AD Connect v Microsoft Cloudu Německo
description: Azure AD Connect integruje vaše místní adresáře do služby Azure Active Directory. To vám umožní poskytovat společnou identitu pro aplikace Microsoft 365, Azure a SaaS integrované s Azure AD.
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
ms.openlocfilehash: 246f8326f7112825de98ee824cf4bab7952a7878
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90014583"
---
# <a name="azure-ad-connect-in-microsoft-cloud-germany---public-preview"></a>Azure AD Connect v Microsoft Cloudu Německo – verze Public Preview
## <a name="introduction"></a>Úvod
Azure AD Connect poskytuje synchronizaci mezi vaší místní službou Active Directory a službou Azure Active Directory.
V současné době je nutné, aby mnoho scénářů v [Microsoft Cloudu Německo](https://azure.microsoft.com/global-infrastructure/germany/
) prováděl operátor. Pokud používáte Microsoft Cloud Německo, musíte mít na paměti následující informace:

* Aby mohlo dojít k úspěšné synchronizaci, je nutné, aby na proxy serveru byly otevřené následující adresy URL:
  
  * *.microsoftonline.de
  * *.windows.net
  * * Seznamy odvolaných certifikátů
* Při přihlášení do adresáře služby Azure AD je nutné použít účet v doméně onmicrosoft.de.

 
## <a name="download"></a>Stáhnout
Službu Azure AD Connect lze stáhnout z okna Azure AD Connect v rámci portálu.  Pomocí pokynů níže vyhledejte okno Azure AD Connect.

### <a name="the-azure-ad-connect-blade"></a>Okno Azure AD Connect
Až se přihlásíte k Azure Portal:

1. Přejděte do Procházet.
2. Vyberte Azure Active Directory.
3. Poté vyberte Azure AD Connect.

Zobrazí se tyto podrobnosti:

![Okno Azure AD Connect](./media/reference-connect-germany/germany1.png)

Následující tabulka popisuje funkce zobrazené v okně.

| Nadpis | Popis |
| --- | --- |
| STAV SYNCHRONIZACE |Uvádí, zda je synchronizace povolená nebo zakázaná. |
| POSLEDNÍ SYNCHRONIZACE |Čas dokončení poslední úspěšné synchronizace. |
| FEDEROVANÉ DOMÉNY |Zobrazuje počet aktuálně nakonfigurovaných federovaných domén. |

## <a name="installation"></a>Instalace
Chcete-li nainstalovat službu Azure AD Connect, můžete využít dokumentaci, kterou najdete [tady](how-to-connect-install-roadmap.md).

## <a name="advanced-features-and-additional-information"></a>Pokročilé funkce a další informace
Další informace o vlastních nastaveních a pokročilých konfiguracích najdete na webu [Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md). Tato stránka poskytuje informace a odkazy na další doprovodné materiály.

