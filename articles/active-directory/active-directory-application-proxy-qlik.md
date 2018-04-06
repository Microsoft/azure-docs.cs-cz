---
title: Proxy aplikace Azure AD a Qlik smysl | Microsoft Docs
description: Zapněte Proxy aplikace na portálu Azure a nainstalujte konektory pro reverzní proxy server.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/03/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e10fefdd3bd46aeb90fd2cfc82d4fee3b17d867b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy aplikací a Qlik smysl 
Azure Proxy aplikace služby Active Directory a Qlik smysl společně společně Ujistěte se, že budete moci snadno poskytnout vzdálený přístup pro vaše nasazení Qlik smysl pomocí Proxy aplikace.  

## <a name="prerequisites"></a>Požadavky 
Zbývající část tohoto scénáře se předpokládá můžete provést následující:
 
- Nakonfigurované [Qlik smysl](https://community.qlik.com/docs/DOC-19822). 
- [Nainstalovaný konektor Proxy aplikace](active-directory-application-proxy-enable.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 
Pokud chcete publikovat QlikSense, musíte publikovat dvě aplikace v Azure.  

### <a name="application-1"></a>Aplikace #1: 
Postupujte podle těchto kroků k publikování aplikace. Pro podrobnější návod kroky 1-8, viz [publikování aplikací pomocí proxy aplikace služby Azure AD](application-proxy-publish-azure-portal.md). 


1. Přihlaste se k portálu Azure jako globální správce. 
2. Vyberte **Azure Active Directory** > **podnikové aplikace, které**. 
3. Vyberte **přidat** v horní části okna. 
4. Vyberte **místní aplikace**. 
5.       Vyplňte požadovaná pole s informacemi o nové aplikace. Použijte následující pokyny pro nastavení: 
    - **Interní adresa URL**: Tato aplikace musí mít interní adresu URL, která je adresa URL QlikSense sám sebe. For example, **https&#58;//demo.qlikemm.com:4244** 
    - **Metoda předběžného ověřování**: Azure Active Directory (ale není požadováno doporučeno) 
1.       Vyberte **přidat** v dolní části okna. Přidání vaší aplikace a otevře se v nabídce rychlý start. 
2.       V nabídce rychlý start, vyberte **přiřadit uživatele pro testování**, a přidejte alespoň jednoho uživatele k aplikaci. Ujistěte se, že tento testovací účet má přístup k aplikaci místní. 
3.       Vyberte **přiřadit** uložit přiřazení uživatelských testu. 
4.       (Volitelné) V okně správy aplikace vyberte jednotné přihlašování. Zvolte **omezené delegování Kerberos** z rozevírací nabídky a výplně se požadovaná pole na základě vaší Qlik konfigurace. Vyberte **Uložit**. 

### <a name="application-2"></a>Aplikace #2: 
Postupujte stejným způsobem jako u aplikace č. 1, s následujícími výjimkami: 

**Krok #5**: interní adresa URL by teď měly být adresa URL QlikSense s ověřování portu používá aplikace. Výchozí hodnota je **4244** pro HTTPS a 4248 pro protokol HTTP. Ex: **https&#58;//demo.qlik.com:4244**</br></br> 
**Krok #10:** nemáte nastavit jednotné přihlašování a nechat **jednotné přihlašování zakázáno**
 
 
## <a name="testing"></a>Testování 
Aplikace je nyní připraven k testování. Přístup k externí adresu URL můžete použít k publikování QlikSense v aplikaci č. 1 a přihlaste se jako uživatel přiřazený k obě aplikace.  

## <a name="next-steps"></a>Další kroky

- [Publikování aplikací pomocí Proxy aplikace](application-proxy-publish-azure-portal.md)
- [Práce s Proxy aplikace konektory](active-directory-application-proxy-connectors-azure-portal.md).
