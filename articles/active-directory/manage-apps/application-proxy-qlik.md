---
title: Proxy aplikací Azure AD a Qlik Sense | Dokumentace Microsoftu
description: Zapněte Proxy aplikace na webu Azure Portal a nainstalujte konektory pro reverzní proxy server.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: abd57a7cefdb41ede914aaaa14967157e1596b9e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55159943"
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
5.       Vyplňte požadovaná pole s informacemi o novou aplikaci. Použijte následující pokyny pro nastavení: 
    - **Interní adresa URL**: Tato aplikace by měla mít interní adresa URL adresy URL QlikSense samotné. Například **https&#58;//demo.qlikemm.com:4244** 
    - **Metoda předběžného ověřování služby**: Azure Active Directory (doporučuje se ale nevyžadováno) 
1.       Vyberte **přidat** v dolní části okna. Přidá se vaše aplikace a otevře se nabídka rychlý start. 
2.       V nabídce rychlý start vyberte **přiřadit uživatele pro testování**, a přidejte aspoň jednoho uživatele k aplikaci. Ujistěte se, že testovacího účtu má přístup k místní aplikaci. 
3.       Vyberte **přiřadit** se uložit přiřazení uživatelských testu. 
4.       (Volitelné) V okně správy aplikace vyberte jednotného přihlašování. Zvolte **omezeného delegování protokolu Kerberos** z rozevírací nabídky a zadejte požadovaná pole na základě vaší konfigurace Qlik. Vyberte **Uložit**. 

### <a name="application-2"></a>Aplikace #2: 
Postupujte stejným způsobem jako u aplikace č. 1, s následujícími výjimkami: 

**Krok #5**: Interní adresa URL by měla nyní být adresa URL QlikSense s port ověřování v aplikaci použít. Výchozí hodnota je **4244** protokoly HTTPS a 4248 pro protokol HTTP. Ex: **https&#58;//demo.qlik.com:4244**</br></br> 
**Krok #10:** Nepoužívejte nastavit jednotné přihlašování a nechte **jednotné přihlašování zakázáno**
 
 
## <a name="testing"></a>Testování 
Vaše aplikace je teď připravený k testování. Přístup k externí adresu URL jste použili k publikování QlikSense v aplikaci č. 1 a přihlášení jako uživatel přiřazený na obě aplikace.  

## <a name="additional-references"></a>Další odkazy
Další informace o publikování Qlik Sense přes Proxy aplikací najdete v článku Qlik komunity: [Azure AD pomocí integrovaného ověřování Windows s použitím omezeného delegování protokolu Kerberos s Qlik Sense](https://community.qlik.com/docs/DOC-20183).

## <a name="next-steps"></a>Další postup

- [Publikování aplikací pomocí Proxy aplikace](application-proxy-add-on-premises-application.md)
- [Práce s konektory Proxy aplikací](application-proxy-connector-groups.md)

