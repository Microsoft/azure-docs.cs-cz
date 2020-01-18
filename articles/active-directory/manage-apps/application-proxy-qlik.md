---
title: Proxy aplikací Azure AD a Qlik Sense | Dokumentace Microsoftu
description: Zapněte Proxy aplikace na webu Azure Portal a nainstalujte konektory pro reverzní proxy server.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45bf71274d8eb1039254cafe48a34587199be724
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76263502"
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy aplikací a Qlik Sense 
Azure Active Directory Application Proxy a Qlik Sense uzavřeli partnerství s společně k zajištění, že budete moct snadno použít k poskytnutí vzdáleného přístupu pro vaše nasazení Qlik Sense Proxy aplikací.  

## <a name="prerequisites"></a>Požadavky 
Zbývající část tento scénář předpokládá, že provedli následující:
 
- Nakonfigurované [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Nainstalovat konektor Proxy aplikací](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 
Chcete-li publikovat QlikSense, je potřeba publikovat dvě aplikace v Azure.  

### <a name="application-1"></a>Aplikace #1: 
Postupujte podle těchto kroků a publikujte svou aplikaci. Pro podrobnější návod k kroky 1-8, viz [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-add-on-premises-application.md). 


1. Přihlaste se k webu Azure portal jako globální správce. 
2. Vyberte **Azure Active Directory** > **podnikové aplikace**. 
3. Vyberte **přidat** v horní části okna. 
4. Vyberte **On-premises application**. 
5. Vyplňte požadovaná pole s informacemi o novou aplikaci. Použijte následující pokyny pro nastavení: 
   - **Interní adresa URL**: Tato aplikace by měla mít interní adresa URL adresy URL QlikSense samotné. Například **https&#58;//demo.qlikemm.com:4244** 
   - **Metoda předběžného ověřování služby**: Azure Active Directory (doporučuje se ale nevyžadováno) 
1. Vyberte **přidat** v dolní části okna. Přidá se vaše aplikace a otevře se nabídka rychlý start. 
2. V nabídce rychlý start vyberte **přiřadit uživatele pro testování**, a přidejte aspoň jednoho uživatele k aplikaci. Ujistěte se, že testovacího účtu má přístup k místní aplikaci. 
3. Vyberte **přiřadit** se uložit přiřazení uživatelských testu. 
4. (Volitelné) V okně správy aplikace vyberte jednotného přihlašování. Zvolte **omezeného delegování protokolu Kerberos** z rozevírací nabídky a zadejte požadovaná pole na základě vaší konfigurace Qlik. Vyberte **Save** (Uložit). 

### <a name="application-2"></a>Aplikace #2: 
Postupujte stejným způsobem jako u aplikace č. 1, s následujícími výjimkami: 

**Krok #5**: interní adresa URL by nyní měly být adresy URL QlikSense s port ověřování v aplikaci použít. Výchozí hodnota je **4244** pro protokol HTTPS a **4248** pro protokol HTTP pro QlikSense vydané verze před duben 2018. Výchozí hodnota pro vydání QlikSense po dubna 2018 je **443** pro HTTPS a **80** pro http.  Ex: **https&#58;//demo.qlik.com:4244**</br></br> 
**Krok #10:** nemáte nastavit jednotné přihlašování a nechte **jednotné přihlašování zakázáno**
 
 
## <a name="testing"></a>Testování 
Vaše aplikace je teď připravený k testování. Přístup k externí adresu URL jste použili k publikování QlikSense v aplikaci č. 1 a přihlášení jako uživatel přiřazený na obě aplikace.  

## <a name="additional-references"></a>Další odkazy
Další informace o publikování Qlikho smyslu pomocí proxy aplikací najdete v článcích komunity Qlik: 
- [Azure AD s integrovaným ověřováním systému Windows s využitím omezeného delegování pomocí protokolu Kerberos s rozpoznáváním Qlik](https://community.qlik.com/docs/DOC-20183)
- [Qlik – integrace rozpoznávání s Azure Proxy aplikací služby AD](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Další kroky

- [Publikování aplikací pomocí Proxy aplikace](application-proxy-add-on-premises-application.md)
- [Práce s konektory Proxy aplikací](application-proxy-connector-groups.md)

