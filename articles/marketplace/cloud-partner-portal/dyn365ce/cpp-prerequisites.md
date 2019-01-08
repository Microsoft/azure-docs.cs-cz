---
title: Dynamics 365 for Customer Engagement nabízí – požadavky na Azure Marketplace | Dokumentace Microsoftu
description: Požadavky pro publikování aplikací Azure nabízet na webu Azure Marketplace.
services: Dynamics 365 for Customer Engagement offer, Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: pbutlerm
ms.openlocfilehash: 4b4859c41e7a3903de68b62e8587f1c85805a782
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082691"
---
# <a name="dynamics-365-for-customer-engagement-prerequisites"></a>Dynamics 365 Customer Engagement předpoklady

Tento článek popisuje technické a podnikové požadavky pro publikování Dynamics 365 Customer Engagement aplikace nabídky na webu AppSource Marketplace.


## <a name="technical-requirements"></a>Technické požadavky

Musí odpovídat vaší Dynamics 365 Customer Engagement aplikace [pokyny pro recenze v app Microsoft AppSource](https://smp-cdn-prod.azureedge.net/documents/AppsourceGuidelines/Microsoft%20AppSource%20app%20review%20guidelines_v5.pdf), což zahrnuje následující požadavky:


|              Požadavek             |        Popis           |
|            ---------------           |      ---------------         |
| Integrace Azure Active Directory   | Aplikace musí povolit Azure Active Directory federované jednotné přihlašování (AAD federované jednotné přihlašování) s souhlasu povolena. Další informace najdete v tématu [získání certifikace AppSource pro Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-get-appsource-certified). |
| Integrace se službami Cloud od Microsoftu (volitelné) | Tam, kde je to nezbytně nutné, vaše aplikace by měla integrace s jinými službami Microsoft Cloud jako služby Microsoft Power BI, Microsoft Flow nebo Microsoft Azure, jako jsou machine learning nebo cognitive services. |
| Obchodní, zaměřuje            |  Aplikace musí zaměřit na obchodní dobře definovaný proces nebo vydat, především cílit na firemním zákazníkům a umožňují uživatelům přihlašovat se pomocí svých pracovních přihlašovacích údajů (uživatelské jméno a heslo).  |
| Období bezplatné zkušební verze a zkušební verze |  Zákazník musí být schopen použít vaši aplikaci zdarma po omezenou dobu: "Get je nyní" pro bezplatné aplikace, "bezplatnou zkušební verzi" pro určité období, demonstrator "Testovací verze" nebo "Kontakt me" žádost o – možnost.  |
| Konfigurace bez nebo s minimálním                 | Aplikace musí být snadné a rychlé ke konfiguraci a nastavení (žádné vývoj nebo přizpůsobení nutné).  |
| Zákaznická podpora                     | Podpora pro vaše aplikace musí obsahovat odkaz podpory zákazníků kde najdete pomoc.  |
| Dostupnost/doba provozu                  | Vaše aplikace musí mít minimálně 99,9 % dostupnost. |
|  |  |


## <a name="business-requirements"></a>Obchodní požadavky

Obchodní požadavky zahrnují následující procesní, smluvní a právní závazky:

* Musíte být zaregistrováni na [Microsoft Partner Network (MPN)](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx) nebo být registrované vydavatele na webu Marketplace cloudu. Pokud jste se nezaregistrovali, postupujte podle kroků v [stát vydavatelem cloudové Tržiště](../../become-publisher.md).  (Třetí krok, použijte [formulář nominace partnera AppSource](https://appsource.microsoft.com/partners/signup)). 

    >[!NOTE]
    >K přihlášení na portál Cloud Partner musí použít stejný účet registrace Microsoft Developer Center. Pro svoje nabídky na webu Azure Marketplace byste měli mít jenom jeden účet Microsoft. Tento účet nesmí být specifické pro jednotlivé služby nebo nabídky.

* Protože AppSource nenabízí možnost publikování povoleno obchodování, je nutné použít aktuální řazení a fakturační infrastruktury bez další investice, změn.
* Zodpovídáte za zpřístupnění technické podpory pro zákazníky přiměřené způsobem. Tato podpora může být zdarma, placená nebo prostřednictvím komunity přístupy.
* Zodpovídáte za licencování váš software a všechny závislosti softwaru třetích stran.
* Měli jste vytvořili přidružené marketingové materiály, jako je například název aplikace oficiální, popis (ve formátu HTML), loga ve formátu PNG (40 x 40, 90 x 90, 115 x 115 a 255 x 115 pixelů) a podmínky použití a zásady ochrany osobních údajů.  


## <a name="next-steps"></a>Další postup

Po splnění těchto požadavků, můžete [vytvořte nabídku Dynamics 365 Customer Engagement](./cpp-create-offer.md) 
