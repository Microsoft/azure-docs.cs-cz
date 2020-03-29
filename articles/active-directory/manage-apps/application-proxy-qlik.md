---
title: Proxy aplikace Azure AD a Qlik Sense| Dokumenty společnosti Microsoft
description: Zapněte proxy aplikace na webu Azure portal a nainstalujte konektory pro reverzní proxy server.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036997"
---
# <a name="application-proxy-and-qlik-sense"></a>Aplikační proxy a Qlik Sense 
Proxy aplikace Azure Active Directory a Qlik Sense spolupracují, aby vám zajistili, že budete moci snadno používat proxy aplikace k poskytování vzdáleného přístupu pro nasazení Qlik Sense.  

## <a name="prerequisites"></a>Požadavky 
Zbývající část tohoto scénáře předpokládá, že jste provedli následující kroky:
 
- Nakonfigurovaný [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Instalace konektoru proxy aplikace](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 
Chcete-li publikovat QlikSense, budete muset publikovat dvě aplikace v Azure.  

### <a name="application-1"></a>Aplikační #1: 
Podle následujících kroků publikujte aplikaci. Podrobnější návod k krokům 1–8 najdete v tématu [Publikování aplikací pomocí proxy aplikace Azure AD](application-proxy-add-on-premises-application.md). 


1. Přihlaste se k webu Azure Portal jako globální správce. 
2. Vyberte aplikace **Azure Active Directory** > **Enterprise**. 
3. V horní části nože vyberte **Přidat.** 
4. Vyberte **místní aplikaci**. 
5. Vyplňte požadovaná pole s informacemi o nové aplikaci. Pro nastavení použijte následující pokyny: 
   - **Interní ADRESA URL**: Tato aplikace by měla mít interní adresu URL, která je samotnou adresou URL QlikSense. Například **https&#58;//demo.qlikemm.com:4244** 
   - **Metoda předběžného ověření:** Služba Azure Active Directory (doporučeno, ale není povinné) 
1. V dolní části nože vyberte **Přidat.** Aplikace je přidána a otevře se nabídka rychlého spuštění. 
2. V nabídce Rychlý start vyberte **Přiřadit uživatele k testování**a přidejte do aplikace alespoň jednoho uživatele. Ujistěte se, že tento testovací účet má přístup k místní aplikaci. 
3. Vyberte **Přiřadit,** chcete-li uložit přiřazení testovacího uživatele. 
4. (Nepovinné) V okně pro správu aplikací vyberte Jednotné přihlašování. Z rozevírací nabídky zvolte **Kerberos Constrained Delegation** a vyplňte požadovaná pole na základě konfigurace Qlik. Vyberte **Uložit**. 

### <a name="application-2"></a>#2 aplikace: 
Postupujte stejným způsobem jako u aplikace #1, s následujícími výjimkami: 

**Krok #5**: Interní adresa URL by nyní měla být adresou URL QlikSense s ověřovacím portem používaným aplikací. Výchozí hodnota je **4244** pro HTTPS a **4248** pro HTTP pro verze QlikSense před dubnem 2018. Výchozí hodnota pro verze QlikSense po dubnu 2018 je **443** pro HTTPS a **80** pro HTTP.  Příklad: **https&#58;//demo.qlik.com:4244**</br></br>
**Krok #10:** Nenastavovat jednotné přihlašování a ponechat **jednotné přihlašování zakázáno**
 
 
## <a name="testing"></a>Testování 
Aplikace je nyní připravena k testování. Získejte přístup k externí adrese URL, kterou jste použili k publikování technologie QlikSense v aplikaci #1, a přihlaste se jako uživatel přiřazený k oběma aplikacím.  

## <a name="additional-references"></a>Další odkazy
Další informace o publikování Qlik Sense s aplikačním proxy serverem naleznete v následujících článcích komunity Qlik: 
- [Azure AD s integrovaným ověřováním systému Windows pomocí delegování omezeného protokolu Kerberos s qlik sense](https://community.qlik.com/docs/DOC-20183)
- [Integrace Qlik Sense s proxy aplikacemi Azure AD](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Další kroky

- [Publikování aplikací pomocí proxy aplikace](application-proxy-add-on-premises-application.md)
- [Práce s konektory proxy aplikací](application-proxy-connector-groups.md)

