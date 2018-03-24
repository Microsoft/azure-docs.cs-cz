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
ms.date: 03/20/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: c34af889f65e0fa91f97655803af099dadcabc5d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy aplikací a Qlik smysl 
Azure Proxy aplikace služby Active Directory a Qlik smysl společně společně Ujistěte se, že budete moci snadno poskytnout vzdálený přístup pro vaše nasazení Qlik smysl pomocí Proxy aplikace.  

## <a name="prerequisites"></a>Požadavky 
Zbývající část tohoto scénáře se předpokládá můžete provést následující:
 
- Nakonfigurované [Qlik smysl](https://help.qlik.com/sense/1.1/Content/Introduction.htm). 
- Nainstalovaný konektor Proxy aplikace 

## <a name="install-an-application-proxy-connector"></a>Instalace konektoru Proxy aplikace 
Pokud už máte Proxy aplikace povolena a máte nainstalovaný konektor, můžete tuto část přeskočit a přesunout na [přidat aplikace do Azure AD pomocí Proxy aplikace](application-proxy-ping-access.md). 

Konektor Proxy aplikace je služba systému Windows Server, která přesměruje přenosy z vzdálení zaměstnanci k publikovaným aplikacím. Podrobné pokyny k instalaci, najdete v části [povolení Proxy aplikace v portálu Azure](active-directory-application-proxy-enable.md). 


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce. 
2. Vyberte služby Azure Active Directory > proxy aplikace. 
3. Vyberte konektor stahování zahájíte stahování konektoru Proxy aplikace. Postupujte podle pokynů pro instalaci. 
 
>[!NOTE]
>Stahování konektoru by měl automaticky povolení Proxy aplikace pro váš adresář, ale pokud ne, můžete si vybrat **povolení Proxy aplikace**. 
 
## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 
Pokud chcete publikovat QlikSense, musíte publikovat dvě aplikace v Azure.  

### <a name="application-1"></a>Aplikace #1: 
Postupujte podle těchto kroků k publikování aplikace. Pro podrobnější návod kroky 1-8, viz [publikování aplikací pomocí proxy aplikace služby Azure AD](application-proxy-publish-azure-portal.md). 


1. Jestliže jste v poslední části, přihlaste se k portálu Azure jako globální správce. 
2. Vyberte **Azure Active Directory** > **podnikové aplikace, které**. 
3. Vyberte **přidat** v horní části okna. 
4. Vyberte **místní aplikace**. 
5.       Vyplňte požadovaná pole s informacemi o nové aplikace. Použijte následující pokyny pro nastavení: 
    - **Interní adresa URL**: Tato aplikace musí mít interní adresu URL, která je adresa URL QlikSense sám sebe. Například **https&#58;//demo.qlikemm.com** 
    - **Metoda předběžného ověřování**: Azure Active Directory (ale není požadováno doporučeno) 
1.       Vyberte **přidat** v dolní části okna. Přidání vaší aplikace a otevře se v nabídce rychlý start. 
2.       V nabídce rychlý start, vyberte **přiřadit uživatele pro testování**, a přidejte alespoň jednoho uživatele k aplikaci. Ujistěte se, že tento testovací účet má přístup k aplikaci místní. 
3.       Vyberte **přiřadit** uložit přiřazení uživatelských testu. 
4.       (Volitelné) V okně správy aplikace vyberte jednotné přihlašování. Zvolte **omezené delegování Kerberos** z rozevírací nabídky a výplně se požadovaná pole na základě vaší Qlik konfigurace. Vyberte **Uložit**. 

### <a name="application-2"></a>Aplikace #2: 
Postupujte stejným způsobem jako u aplikace č. 1, s následujícími výjimkami: 

**Krok #5**: interní adresa URL by teď měly být adresa URL QlikSense s ověřování portu používá aplikace. Výchozí hodnota je **4244** pro HTTPS a 4248 pro protokol HTTP. Ex: **https&#58;//demo.qlik.com:4244** 
**krok 10:** nemáte nastavit jednotné přihlašování a nechat **jednotné přihlašování zakázáno**
 
 
## <a name="testing"></a>Testování 
Aplikace je nyní připraven k testování. Přístup k externí adresu URL můžete použít k publikování QlikSense v aplikaci č. 1 a přihlaste se jako uživatel přiřazený k obě aplikace.  

## <a name="next-steps"></a>Další kroky

- [Publikování aplikací pomocí Proxy aplikace](application-proxy-publish-azure-portal.md)
- [Práce s Proxy aplikace konektory](active-directory-application-proxy-connectors-azure-portal.md).
