---
title: Vlastními doménami v Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Správa vlastních domén v Azure AD Application Proxy tak, aby adresa URL pro aplikaci je stejný bez ohledu na to, kde vaši uživatelé k němu přístup.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d721e750ddb77ba293643ad978cecf4bd2dbac0
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68618810"
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Práce s vlastními doménami v Proxy aplikací Azure AD

Při publikování aplikace prostřednictvím Azure Active Directory Application Proxy můžete vytvořit externí adresu URL pro vaše uživatele zobrazíte, když pracují vzdáleně. Tuto adresu URL získá výchozí doménu *yourtenant.msappproxy.net*. Například pokud jste publikovali aplikaci s názvem výdaje a váš tenant je s názvem Contoso, pak je externí adresa URL `https://expenses-contoso.msappproxy.net`. Pokud chcete použít vlastní název domény, konfigurace vlastní domény pro vaši aplikaci. 

Doporučujeme, abyste nastavili vlastní domény pro vaše aplikace, kdykoli je to možné. Mezi výhody vlastních domén, patří:

- Uživatelé dostanou k aplikaci pomocí stejné adresy URL, ať už pracují uvnitř nebo mimo síť.
- Pokud všechny aplikace mají stejné interní a externí adresy URL, odkazy v jedné aplikaci, které odkazují na jiný dál fungovat i mimo podnikovou síť. 
- Ovládací prvek brandingu a adresy URL, které chcete vytvořit. 


## <a name="configure-a-custom-domain"></a>Konfigurace vlastní domény

### <a name="prerequisites"></a>Požadavky

Než začnete konfigurovat vlastní doménu, ujistěte se, že máte připravené následující požadavky: 
- A [ověřené domény do Azure Active Directory](../fundamentals/add-custom-domain.md).
- Vlastní certifikát pro doménu, ve formě souboru PFX. 
- Aplikace v místním [publikované prostřednictvím Proxy aplikací](application-proxy-add-on-premises-application.md).

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
9. Zkontrolujte, jestli je záznam DNS správně nakonfigurovaný pomocí příkazu [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) a zjistěte, jestli je vaše externí adresa URL dostupná, a doména msapproxy.NET se zobrazí jako alias.

>[!TIP] 
>Stačí nahrát jeden certifikát na vlastní doménu. Až nahrajete certifikát, můžete vlastní doménu, při publikování nové aplikace a není nutné provést další konfiguraci, s výjimkou záznamu DNS. 

## <a name="manage-certificates"></a>Správa certifikátů

### <a name="certificate-format"></a>Formát certifikátu
Neexistuje žádné omezení na certifikátu podpisu metody. Šifrování ECC (Elliptic Curve), alternativní název předmětu (SAN) a další běžné typy certifikátů jsou všechny podporované. 

Certifikát se zástupným znakem slouží jako zástupný znak odpovídá požadované externí adresu URL.

Certifikáty vydané vaší vlastní infrastrukturou veřejných klíčů (PKI) je možné použít, pokud je v klientských zařízeních nainstalovaný řetěz certifikátů. Intune se dá použít k nasazení těchto certifikátů na spravovaná zařízení. U nespravovaných zařízení se tyto certifikáty musí nainstalovat ručně.

### <a name="changing-the-domain"></a>Změna domény
V rozevíracím seznamu externí adresu URL pro vaši aplikaci se zobrazí všechny ověřené domény. Chcete-li změnit doménu, jen aktualizujte toto pole pro aplikaci. Pokud chcete doménu není v seznamu [ho přidat jako ověřenou doménou](../fundamentals/add-custom-domain.md). Pokud vyberete doméně, která nebude mít přidružený certifikát dosud, postupujte podle kroků 5 až 7 pro přidání certifikátu. Ujistěte se, že aktualizujte záznam DNS pro přesměrování z nové externí adresy URL. 

### <a name="certificate-management"></a>Správa certifikátů
Pokud aplikace sdílí externího hostitele, můžete použít stejný certifikát pro více aplikací. 

Se zobrazí upozornění, když vyprší platnost certifikátu, s výzvou k nahrát jiný certifikát prostřednictvím portálu. Pokud je certifikát odvolán, může vašim uživatelům zobrazovat upozornění zabezpečení při přístupu k aplikaci. Neprovádějte jsme kontroly odvolání certifikátů.  Aktualizovat certifikát pro danou aplikaci, přejděte do aplikace a postupujte podle kroků 5 až 7 pro konfiguraci vlastní domény na publikované aplikace nahrát nový certifikát. Pokud starý certifikát není používán jinými aplikacemi, se automaticky odstraní. 

Aktuálně je všechny správy certifikátů prostřednictvím stránky jednotlivých aplikací, které potřebujete ke správě certifikátů v souvislosti s příslušným aplikacím. 

## <a name="next-steps"></a>Další postup
* [Povolit jednotné přihlašování](application-proxy-configure-single-sign-on-with-kcd.md) pro aplikace publikované pomocí ověřování Azure AD.
* [Povolte podmíněný přístup](application-proxy-integrate-with-sharepoint-server.md) k publikovaným aplikacím.
* [Přidání vlastního názvu domény do Azure AD](../fundamentals/add-custom-domain.md)


