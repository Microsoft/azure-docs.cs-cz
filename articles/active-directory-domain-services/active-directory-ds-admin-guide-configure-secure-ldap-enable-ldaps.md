---
title: Povolení zabezpečeného protokolu LDAP (LDAPS) ve službě Azure AD Domain Services | Dokumentace Microsoftu
description: Povolit Secure LDAP (LDAPS) pro spravované domény služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: df189e405dcd5277c1ccbd94e9d5d302660be79b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55171418"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Povolení zabezpečeného protokolu LDAP (LDAPS) pro spravované domény služby Azure AD Domain Services

## <a name="before-you-begin"></a>Před zahájením
Kompletní [úloha 2 – exportujte certifikát protokolu secure LDAP pro. Soubor PFX](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md).


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Úloha 3: Povolení protokolu LDAPS pro spravované domény pomocí webu Azure portal
Pokud chcete povolit protokol secure LDAP, proveďte následující kroky konfigurace:

1. Přejděte  **[webu Azure portal](https://portal.azure.com)**.

2. Vyhledejte "domain services" v **vyhledat prostředky** vyhledávacího pole. Vyberte **Azure AD Domain Services** z výsledku hledání. **Azure AD Domain Services** stránce uvedeny vaší spravované domény.

    ![Najít zřizuje spravované domény](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klikněte na název spravované domény (například "contoso100.com") zobrazíte další podrobnosti o doméně.

    ![Domain Services – Stav zřizování](./media/getting-started/domain-services-provisioning-state.png)

3. Klikněte na tlačítko **protokolu Secure LDAP** v navigačním podokně.

    ![Doménové služby – stránka protokol Secure LDAP.](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Ve výchozím nastavení je zakázána protokolu Secure LDAP pro spravovanou doménu. Přepnout **protokol Secure LDAP** k **povolit**.

    ![Povolení protokolu LDAPS](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Ve výchozím nastavení je zakázán přístup ke spravované doméně přes internet secure LDAP. Přepnout **povolit protokolu Secure LDAP přes internet** k **povolit**, pokud je potřeba.

    > [!WARNING]
    > Když povolíte přístup protokolu secure LDAP přes internet, vaše doména je náchylný na útoky hrubou silou hesla přes internet. Proto doporučujeme nastavit skupinu zabezpečení sítě pro uzamčení přístupu k požadovaných zdrojových rozsahů IP adres. Přečtěte si pokyny k [uzamknutí LDAPS přístup ke spravované doméně přes internet](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Klikněte na ikonu následující složky **. Soubor PFX s certifikátem protokolu secure LDAP**. Zadejte cestu k souboru PFX s certifikátem pro přístup ke spravované doméně secure LDAP.

7. Zadejte **heslo k dešifrování. Soubor PFX**. Zadejte stejné heslo, které jste použili při exportu certifikátu do souboru PFX.

8. Jakmile budete hotovi, klikněte na tlačítko **Uložit** tlačítko.

9. Zobrazí se oznámení, která informuje o tom, že je protokol secure LDAP je konfigurován pro spravovanou doménu. Než se tato operace dokončí, nelze upravovat ostatní nastavení pro doménu.

    ![Konfigurace zabezpečeného protokolu LDAP pro spravovanou doménu](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Povolení protokolu LDAPS pro spravované domény trvá asi 10 až 15 minut. Pokud zadaný certifikát protokolu secure LDAP neodpovídá požadovaná kritéria, protokol secure LDAP není povolená pro svůj adresář a zobrazit informace o selhání. Například je název domény nesprávný, certifikátu již vypršela nebo brzy vyprší. V takovém případě zkuste to znovu s platným certifikátem.
>
>

## <a name="next-step"></a>Další krok
[Úloha 4: Konfigurace DNS pro přístup ke spravované doméně z internetu](active-directory-ds-ldaps-configure-dns.md)
