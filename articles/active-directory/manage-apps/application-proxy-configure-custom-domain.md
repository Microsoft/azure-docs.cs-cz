---
title: Vlastními doménami v Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Správa vlastních domén v Azure AD Application Proxy tak, aby adresa URL pro aplikaci je stejný bez ohledu na to, kde vaši uživatelé k němu přístup.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 090df19861e00a99f0bb63bf20eb2f26dc6761fd
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369034"
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Práce s vlastními doménami v Proxy aplikací Azure AD

Při publikování aplikace prostřednictvím Azure Active Directory Application Proxy můžete vytvořit externí adresu URL pro vaše uživatele zobrazíte, když pracují vzdáleně. Tuto adresu URL získá výchozí doménu *yourtenant.msappproxy.net*. Například pokud jste publikovali aplikaci s názvem výdaje a váš tenant je s názvem Contoso, pak je externí adresa URL https://expenses-contoso.msappproxy.net. Pokud chcete použít vlastní název domény, konfigurace vlastní domény pro vaši aplikaci. 

Doporučujeme, abyste nastavili vlastní domény pro vaše aplikace, kdykoli je to možné. Mezi výhody vlastních domén, patří:

- Uživatelé dostanou k aplikaci pomocí stejné adresy URL, ať už pracují uvnitř nebo mimo síť.
- Pokud všechny aplikace mají stejné interní a externí adresy URL, odkazy v jedné aplikaci, které odkazují na jiný dál fungovat i mimo podnikovou síť. 
- Ovládací prvek brandingu a adresy URL, které chcete vytvořit. 


## <a name="configure-a-custom-domain"></a>Konfigurace vlastní domény

### <a name="prerequisites"></a>Požadavky

Než začnete konfigurovat vlastní doménu, ujistěte se, že máte připravené následující požadavky: 
- A [ověřené domény do Azure Active Directory](../fundamentals/add-custom-domain.md).
- Vlastní certifikát pro doménu, ve formě souboru PFX. 
- Aplikace v místním [publikované prostřednictvím Proxy aplikací](application-proxy-publish-azure-portal.md).

### <a name="configure-your-custom-domain"></a>Konfigurace vlastní domény

Až budete mít tyto tři požadavky na Připraveno, použijte následující postup nastavení vlastní domény:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace** a zvolte aplikaci, kterou chcete spravovat.
3. Vyberte **Proxy aplikací**. 
4. V poli externí adresu URL pomocí rozevíracího seznamu vyberte vaši vlastní doménu. Pokud se nezobrazí vaši doménu v seznamu, pak ho nebyla ověřena ještě. 
5. Vyberte **uložit**
5. **Certifikát** nepovolíte pole, která byla zakázána. Toto políčko zaškrtněte. 

   ![Klikněte na tlačítko pro odeslání certifikátu](./media/application-proxy-configure-custom-domain/certificate.png)

   Pokud jste již nahráli certifikát pro tuto doménu, certifikát pole zobrazí informace o certifikátu. 

6. Odeslat certifikát PFX a heslo pro certifikát. 
7. Vyberte **Uložit** uložte provedené změny. 
8. Přidat [záznam DNS](../../dns/dns-operations-recordsets-portal.md) nový externí adresu URL, který přesměruje na doménou msappproxy.net. 

>[!TIP] 
>Stačí nahrát jeden certifikát na vlastní doménu. Až nahrajete certifikát, můžete vlastní doménu, při publikování nové aplikace a není nutné provést další konfiguraci, s výjimkou záznamu DNS. 

## <a name="manage-certificates"></a>Správa certifikátů

### <a name="certificate-format"></a>Formát certifikátu
Neexistuje žádné omezení na certifikátu podpisu metody. Šifrování ECC (Elliptic Curve), alternativní název předmětu (SAN) a další běžné typy certifikátů jsou všechny podporované. 

Certifikát se zástupným znakem slouží jako zástupný znak odpovídá požadované externí adresu URL. 

### <a name="changing-the-domain"></a>Změna domény
V rozevíracím seznamu externí adresu URL pro vaši aplikaci se zobrazí všechny ověřené domény. Chcete-li změnit doménu, jen aktualizujte toto pole pro aplikaci. Pokud chcete doménu není v seznamu [ho přidat jako ověřenou doménou](../fundamentals/add-custom-domain.md). Pokud vyberete doméně, která nebude mít přidružený certifikát dosud, postupujte podle kroků 5 až 7 pro přidání certifikátu. Ujistěte se, že aktualizujte záznam DNS pro přesměrování z nové externí adresy URL. 

### <a name="certificate-management"></a>Správa certifikátů
Pokud aplikace sdílí externího hostitele, můžete použít stejný certifikát pro více aplikací. 

Se zobrazí upozornění, když vyprší platnost certifikátu, s výzvou k nahrát jiný certifikát prostřednictvím portálu. Pokud je certifikát odvolán, může vašim uživatelům zobrazovat upozornění zabezpečení při přístupu k aplikaci. Neprovádějte jsme kontroly odvolání certifikátů.  Aktualizovat certifikát pro danou aplikaci, přejděte do aplikace a postupujte podle kroků 5 až 7 pro konfiguraci vlastní domény na publikované aplikace nahrát nový certifikát. Pokud starý certifikát není používán jinými aplikacemi, se automaticky odstraní. 

Aktuálně je všechny správy certifikátů prostřednictvím stránky jednotlivých aplikací, které potřebujete ke správě certifikátů v souvislosti s příslušným aplikacím. 

## <a name="next-steps"></a>Další postup
* [Povolit jednotné přihlašování](application-proxy-configure-single-sign-on-with-kcd.md) pro aplikace publikované pomocí ověřování Azure AD.
* [Zapnout zásady podmíněného přístupu](application-proxy-integrate-with-sharepoint-server.md) do publikované aplikace.
* [Přidání vlastního názvu domény do Azure AD](../fundamentals/add-custom-domain.md)


