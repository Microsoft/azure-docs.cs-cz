---
title: Aplikace Azure AD proxy a Qlik smysl | Microsoft Docs
description: Zapněte proxy aplikace v Azure Portal a nainstalujte konektory pro reverzní proxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a03f3c57d3fbc7efe20055343eab3db33aa8194
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259080"
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy aplikace a Qlik – smysl 
Proxy aplikací služby Azure Active Directory a Qliký smysl spolupracovali společně, aby bylo zajištěno, že k zajištění vzdáleného přístupu pro nasazení rozpoznávání Qlik budete moct snadno používat proxy aplikace.  

## <a name="prerequisites"></a>Požadavky 
Zbývající část tohoto scénáře předpokládá, že jste provedli následující akce:
 
- Byl nakonfigurován [Qlik smysl](https://community.qlik.com/docs/DOC-19822). 
- [Instalace konektoru proxy aplikací](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 
K publikování QlikSense budete muset v Azure publikovat dvě aplikace.  

### <a name="application-1"></a>#1 aplikace: 
Při publikování aplikace postupujte podle těchto kroků. Podrobnější návod k krokům 1-8 najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md). 


1. Přihlaste se k webu Azure Portal jako globální správce. 
2. Vyberte **Azure Active Directory**  >  **podnikových aplikací**. 
3. V horní části okna vyberte **Přidat** . 
4. Vyberte **místní aplikaci**. 
5. Vyplňte požadovaná pole informacemi o vaší nové aplikaci. Pro nastavení použijte následující pokyny: 
   - **Interní adresa URL**: Tato aplikace by měla mít interní adresu URL, která je QlikSense adresou URL. Například **https&#58;//demo.qlikemm.com:4244** 
   - **Metoda** předběžného ověřování: Azure Active Directory (doporučeno, ale není nutné) 
1. V dolní části okna vyberte **Přidat** . Vaše aplikace se přidá a otevře se nabídka rychlý Start. 
2. V nabídce rychlý Start vyberte **přiřadit uživatele pro testování** a přidejte alespoň jednoho uživatele do aplikace. Ujistěte se, že tento testovací účet má přístup k místní aplikaci. 
3. Vyberte **přiřadit** a uložte přiřazení testovacího uživatele. 
4. Volitelné V okně Správa aplikací vyberte jednotné přihlašování. V rozevírací nabídce vyberte **omezené delegování Kerberos** a vyplňte požadovaná pole na základě konfigurace Qlik. Vyberte **Uložit**. 

### <a name="application-2"></a>#2 aplikace: 
Použijte stejný postup jako u #1 aplikace, s následujícími výjimkami: 

**Krok #5**: interní adresa URL by teď měla být adresa URL QlikSense s portem ověřování používaným aplikací. Výchozí hodnota je **4244** pro protokol HTTPS a **4248** pro protokol HTTP pro QlikSense vydané verze před duben 2018. Výchozí hodnota pro vydání QlikSense po dubna 2018 je **443** pro HTTPS a **80** pro http.  Např.: **https&#58;//demo.Qlik.com:4244**</br></br>
**#10 kroku:** Nepoužívejte jednotné přihlašování a ponechte **zakázané jednotné přihlášení** .
 
 
## <a name="testing"></a>Testování 
Vaše aplikace je teď připravená k testování. Přístup k externí adrese URL, kterou jste použili k publikování QlikSense v aplikaci #1 a přihlaste se jako uživatel přiřazený k oběma aplikacím.  

## <a name="additional-references"></a>Další odkazy
Další informace o publikování Qlikho smyslu pomocí proxy aplikací najdete v článcích komunity Qlik: 
- [Azure AD s integrovaným ověřováním systému Windows s využitím omezeného delegování pomocí protokolu Kerberos s rozpoznáváním Qlik](https://community.qlik.com/docs/DOC-20183)
- [Qlik – integrace rozpoznávání s Azure Proxy aplikací služby AD](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Další kroky

- [Publikování aplikací pomocí proxy aplikace](application-proxy-add-on-premises-application.md)
- [Práce s konektory proxy aplikací](application-proxy-connector-groups.md)

