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
ms.openlocfilehash: 2edf63da0fd09f829f936b54eb088c34dfe029a1
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79036997"
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy aplikací a Qlik Sense 
Azure Active Directory Application Proxy a Qlik Sense uzavřeli partnerství s společně k zajištění, že budete moct snadno použít k poskytnutí vzdáleného přístupu pro vaše nasazení Qlik Sense Proxy aplikací.  

## <a name="prerequisites"></a>Předpoklady 
Zbývající část tento scénář předpokládá, že provedli následující:
 
- Byl nakonfigurován [Qlik smysl](https://community.qlik.com/docs/DOC-19822). 
- [Instalace konektoru proxy aplikací](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 
Chcete-li publikovat QlikSense, je potřeba publikovat dvě aplikace v Azure.  

### <a name="application-1"></a>Aplikace #1: 
Postupujte podle těchto kroků a publikujte svou aplikaci. Podrobnější návod k krokům 1-8 najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md). 


1. Přihlaste se k webu Azure portal jako globální správce. 
2. Vyberte **Azure Active Directory** > **podnikových aplikací**. 
3. V horní části okna vyberte **Přidat** . 
4. Vyberte **místní aplikaci**. 
5. Vyplňte požadovaná pole s informacemi o novou aplikaci. Použijte následující pokyny pro nastavení: 
   - **Interní adresa URL**: Tato aplikace by měla mít interní adresu URL, která je QlikSense adresou URL. Například **https&#58;//demo.qlikemm.com:4244** 
   - **Metoda**předběžného ověřování: Azure Active Directory (doporučeno, ale není nutné) 
1. V dolní části okna vyberte **Přidat** . Přidá se vaše aplikace a otevře se nabídka rychlý start. 
2. V nabídce rychlý Start vyberte **přiřadit uživatele pro testování**a přidejte alespoň jednoho uživatele do aplikace. Ujistěte se, že testovacího účtu má přístup k místní aplikaci. 
3. Vyberte **přiřadit** a uložte přiřazení testovacího uživatele. 
4. (Volitelné) V okně správy aplikace vyberte jednotného přihlašování. V rozevírací nabídce vyberte **omezené delegování Kerberos** a vyplňte požadovaná pole na základě konfigurace Qlik. Vyberte **Save** (Uložit). 

### <a name="application-2"></a>Aplikace #2: 
Postupujte stejným způsobem jako u aplikace č. 1, s následujícími výjimkami: 

**Krok #5**: interní adresa URL by teď měla být adresa URL QlikSense s portem ověřování používaným aplikací. Výchozí hodnota je **4244** pro protokol HTTPS a **4248** pro protokol HTTP pro QlikSense vydané verze před duben 2018. Výchozí hodnota pro vydání QlikSense po dubna 2018 je **443** pro HTTPS a **80** pro http.  Např. **:&#58;https//demo.Qlik.com:4244**</br></br>
**#10 kroku:** Nepoužívejte jednotné přihlašování a ponechte **zakázané jednotné přihlášení** .
 
 
## <a name="testing"></a>Testování 
Vaše aplikace je teď připravený k testování. Přístup k externí adresu URL jste použili k publikování QlikSense v aplikaci č. 1 a přihlášení jako uživatel přiřazený na obě aplikace.  

## <a name="additional-references"></a>Další odkazy
Další informace o publikování Qlikho smyslu pomocí proxy aplikací najdete v článcích komunity Qlik: 
- [Azure AD s integrovaným ověřováním systému Windows s využitím omezeného delegování pomocí protokolu Kerberos s rozpoznáváním Qlik](https://community.qlik.com/docs/DOC-20183)
- [Qlik – integrace rozpoznávání s Azure Proxy aplikací služby AD](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Další kroky

- [Publikování aplikací pomocí proxy aplikací](application-proxy-add-on-premises-application.md)
- [Práce s konektory proxy aplikací](application-proxy-connector-groups.md)

