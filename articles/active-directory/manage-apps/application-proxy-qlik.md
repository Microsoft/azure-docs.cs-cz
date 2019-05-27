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
ms.openlocfilehash: 8f54e08e6c3b7b673541f124a90f32dbc860fa44
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65859539"
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
4. (Volitelné) V okně správy aplikace vyberte jednotného přihlašování. Zvolte **omezeného delegování protokolu Kerberos** z rozevírací nabídky a zadejte požadovaná pole na základě vaší konfigurace Qlik. Vyberte **Uložit**. 

### <a name="application-2"></a>Aplikace #2: 
Postupujte stejným způsobem jako u aplikace č. 1, s následujícími výjimkami: 

**Krok #5**: Interní adresa URL by měla nyní být adresa URL QlikSense s port ověřování v aplikaci použít. Výchozí hodnota je **4244** protokoly HTTPS a 4248 pro protokol HTTP. Ex: **https&#58;//demo.qlik.com:4244**</br></br> 
**Krok #10:** Nepoužívejte nastavit jednotné přihlašování a nechte **jednotné přihlašování zakázáno**
 
 
## <a name="testing"></a>Testování 
Vaše aplikace je teď připravený k testování. Přístup k externí adresu URL jste použili k publikování QlikSense v aplikaci č. 1 a přihlášení jako uživatel přiřazený na obě aplikace.  

## <a name="additional-references"></a>Další odkazy
Další informace o publikování Qlik Sense pomocí Proxy aplikace najdete v následujících článcích komunity Qlik: 
- [Azure AD pomocí integrovaného ověřování Windows s použitím omezeného delegování protokolu Kerberos s Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Qlik Sense integrace s Azure AD Application Proxy](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Další postup

- [Publikování aplikací pomocí Proxy aplikace](application-proxy-add-on-premises-application.md)
- [Práce s konektory Proxy aplikací](application-proxy-connector-groups.md)

